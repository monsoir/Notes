# 渲染原理

[iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/) 这篇文章干货满满，以前看得很迷茫，现在再看，清晰了很多。将其中部分的原理介绍整理一下，方便以后快查和回顾

## CRT 显示器原理

- 显示方式
    - CRT 电子枪从上倒下一行行扫描
    - 扫描完成后显示器呈现一帧画面
    - 电子枪回到初始位置继续下一次扫描
- 显示器的显示过程与系统的视频控制器同步，显示器将会硬件时钟产生一系列定时信号
    - 电子枪换到新行，准备扫描时，发出水平同步信号 HSync
    - 一帧画面绘制完成，电子枪复位，准备扫描时，发出垂直同步信号 VSync
    - 显示器以固定频率刷新，频率就是 VSync 的频率

## CPU, GPU, 显示器协同工作

- CPU 计算好显示内容提交到 GPU
- GPU 渲染完成后将结果放入帧缓冲区
- 视频控制器按照 VSync 信号，逐行读取帧缓冲区的数据，经过数模转换传递给显示器

## iOS 设备显示策略

iOS 设备使用双缓存 + 垂直同步

### 双缓冲

- 显示系统使用两个帧 _缓冲区_ ，设为 A, B
- GPU 将渲染好的一帧结果放入其中一个 _缓冲区_ ，设为 A, 供 _视频控制器_ 读取
- GPU 又将下一帧渲染好了，结果放到另一个缓冲区，设为 B, 同时，将 _视频控制器_ 的指针指向 B

> 使用两个 _缓冲区_ ，提高效率
> 带来的问题： _视频控制器_ 读取数据未完成，GPU 将 _视频控制器_ 的指针切换到另一个 _缓冲区_ ，造成旧的帧数据与新的帧数据重合，使得画面违和

### 垂直同步

CPU 将会等待上面提到的 VSync 信号，接收到后才会进行新一帧的渲染和缓冲区更新

> 带来的问题：消费更多的计算资源，带来部分的延迟

## 界面卡顿原理

### 更进一步的 CPU 与 GPU 协同工作原理

CPU 阶段

- 接收到 VSync 信号后，系统图形服务通过 CADisplayLink 等机制通知 App
- App 主线程开始在 CPU 中计算显示内容
    - 视图的创建
    - 布局计算
    - 图片解码
    - 文本绘制
    - ...
- CPU 会将计算好的内容提交到 GPU

---

GPU 阶段

- GPU 进行变换、合成、渲染
- GPU 会把渲染结果提交到帧缓冲区去，等待下一次 VSync 信号到来时显示到屏幕上

由工作流程可以知道，由于 _垂直同步_ 机制，如果在一个 VSync 时间内，CPU 或 GPU 没有完成内容的提交，该一帧(B)的数据就会被丢弃，而上一帧(A)的内容保持不变，这时候又有新的一帧(C)补充过来，于是，就有了卡顿，跳帧的效果

## CPU 资源消耗在哪里

### 对象创建

消耗

- 分配内存
- 调整属性
- 读取文件
- Storyboard 创建视图对象

---

建议

- 尽量使用 CALayer, 比 UIView 轻量
- 不涉及 UI 操作的，放到后台线程
- 包含了 CALayer 的控件，必须使用主线程创建和操作

### 对象调整

#### CALayer 与 UIView 的关系

由于 UIView 相关的显示属性(frame, bounds, transform) 都是 CALayer 属性映射过来，所以所有的起因都起于 CALayer

#### CALayer 的属性

- CALayer 内部没有属性
- 调用属性方法时，通过运行时的 `resolveInstanceMethod` 未对象临时添加方法，并把属性值存放到内部的 Dictionary 中
- 同时通知 delegate, 创建动画，等等

建议：

- 性能优化时，尽量避免调整视图层次，添加和移除视图

### 对象销毁

不得已的时候，可以在后台释放对象

释放当前对实例的引用，并把实例扔到后台线程随便调用一下方法

```objc
NSArray *tmp = self.array;
self.array = nil;
dispatch_async(queue, ^{
    [tmp class];
});
```

### 布局计算

建议

- 在后台线程提前计算好视图布局，并对布局进行缓存
    - 我猜应该是说，把视图的坐标，大小在后台提前计算好，毕竟赋值的时候还是要在主线程
- 一次性调整好视图的属性，不要多次频繁计算和调整

### 自动布局

对复杂视图来说会有严重的性能问题

建议：

- 手动设置 frame 属性，配合快捷属性的工具方法
- ComponentKie
- AsyncDisplayKit

### 文本计算

#### 文本显示原理

- 屏幕上看到的文本内容，都是通过 CoreText 排版，绘制为 Bitmap 显示
    - UIWebView
    - UILabel
    - UITextView
- 排版和绘制都是在主线程进行
    - 文本量太大时，会造成 CPU 压力过大

文本过多时，应使用底层的文本库进行自定义文本控件的定义，如 CoreText, CoreText 使用起来麻烦，但是可以直接获取到文本的宽高信息，占用内存也比较小

### 图片解码

#### 图片解码原理

- 使用 UIImage 或 CGImageSource 创建的图片，图片数据不会立刻解码
- 图片设置到 UIImageView 或 CALayer.contents, 在 CALayer 数据被提交到 GPU 前，图片数据才会得到解码，这些都发生在主线程上，**不可避免的**

建议：

- 后台线程把图片绘制到 CGBitmapContext 中，从 Bitmap 中直接创建图片

### 图像绘制

> 图像的绘制通常是指用那些以 CG 开头的方法把图像绘制到画布中，然后从画布创建图片并显示这样一个过程。这个最常见的地方就是 [UIView drawRect:] 里面了。由于 CoreGraphic 方法通常都是线程安全的，所以图像的绘制可以很容易的放到后台线程进行

```objc
- (void)display {
    dispatch_async(backgroundQueue, ^{
        CGContextRef ctx = CGBitmapContextCreate(...);
        // draw in context...
        CGImageRef img = CGBitmapContextCreateImage(ctx);
        CFRelease(ctx);
        dispatch_async(mainQueue, ^{
            layer.contents = img;
        });
    });
}
```

## GPU 消耗在哪里

GPU 的工作稍微简单：

- 接收提交的纹理，定点描述
- 执行变换
- 混合，渲染
- 输出到屏幕

### 纹理渲染

- 所有的 Bitmap(包括了图片、文本、栅格化的内容)，都要从内存提交到显存，绑定为 GPU Texture.
- 两个过程，数据从内存提交到显存，GPU 调整和渲染 Texture, 需要消耗 GPU 大量资源
- 图片过大，超过 GPU 最大纹理尺寸时，图片就需要 CPU 预处理，造成了 CPU 和 GPU 的消耗增大了

建议：

- 短时间显示大量图片时，尽可能将多张图片合并为一张显示
    - 显示大量图片的 table view 快速滑动时，GPU 占用非常高

### 视图混合

- 多个视图(其实是 CALayer)重叠显示时，GPU 会现将图层混合
- 若视图机构太复杂，将消耗过多的 GPU 资源

建议：

- 尽量减少视图数量和层次
- 不透明的视图表明 opaque 属性，避免无用的 Alpha 通道合成
- 过多的视图，可以将多个视图预先渲染为一张图片显示

### 图形生成

- 离屏渲染 offscreen rendering 发生在 GPU 中
    - CALayer 的 border, 圆角、阴影、遮罩(mask)
    - CAShapeLayer 矢量图形显示

建议：

- 当出现大量圆角的 CALayer 时，可以开启 CALayer.shouldRasterize 属性
    - 将原本离屏渲染的操作转移到 CPU 上
- 使用一张绘制好圆角的图片覆盖到原本的视图上，模拟圆角效果
- 需要显示的图形在后台线程绘制为图片，避免使用圆角、阴影、遮罩等属性

## References

- [iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)


