# Python 图像处理

- 在 Python 中，处理图像可以使用第三方模块 Pillow

- [Pillow](#pillow)
    - [获取颜色的 RGBA 值](#获取颜色的-rgba-值)
    - [加载图像](#加载图像)
    - [获取图像数据](#获取图像数据)
    - [新建图像](#新建图像)
    - [裁剪图片](#裁剪图片)
    - [复制和粘贴图像到其他图像](#复制和粘贴图像到其他图像)
    - [调整图片大小](#调整图片大小)
    - [旋转和翻转图像](#旋转和翻转图像)
    - [更改单个像素](#更改单个像素)

## Pillow

- 在 Pillow 中，一个 RGBA 值表示为 4 个整数值的元组，如 (255, 0, 0, 255)
- 在 Pillow 中，通常需要一个矩形的数据，这个矩形的数据由一个 4 个整数组成的元组表示：
    - 左，矩形最左边的 x 坐标
    - 顶，矩形的顶边 y 坐标
    - 右，矩形最右边的 x 坐标，实际矩形的范围不包括这个值
    - 底，矩形的底边 y 坐标，实际矩形的范围不包括这个值
    - 原点位于矩形左上角，向下为 y 的正方向，向右为 x 的正方向

- 安装 Pillow

    ```shell
    pip install pillow
    ```

- Pillow 的模块名为 PIL

### 获取颜色的 RGBA 值

```py
from PIL import ImageColor
color = ImageColor.getcolor('red', 'RGBA')
```

- 返回的 color 是使用 4 个整数值的元组
- 传入的颜色名称不限定大小写

### 加载图像

```py
from PIL import Image

anImage = Image.open(imagePath)
```

- 由于设计问题，引入 Image 须使用 `from PIL import Image`
- 返回的 anImage 是一个 Image 对象，在这个对象上，可以
    - save(), 保存对图片的任何修改，对任何文件都适用
    - 旋转，调整大小，裁剪，绘画等操作

### 获取图像数据

```py
anImage.size # 获取大小，返回的是 (width, height) 元组

anImage.filename # 获取文件名，包含后缀名

anImage.format # 获取图片格式

anImage.format_description # 获取图片格式的定义

anImage.save('xxx.jpg') # 可以直接指定后缀名来对图像进行格式转换
```

### 新建图像

```py
from PIL import Image

im = Image.new('RGBA', (100, 200), 'purple')
im.save('image.png')
```

参数

- 颜色模式
- 大小
- 起始背景颜色，可以是
    - RGBA 值元组，或使用 `ImageColor.getColor()` 来获取颜色值元组
    - 颜色名称字符串

> 若不指定颜色，则默认是 (0, 0, 0, 0), 即透明色


### 裁剪图片

使用 `crop()` 方法

```py
croppedImage = anImage.crop(cropRect)
croppedImage.save(imageName)
```

- 参数为一个开篇说到的矩形数据
- `crop()` 方法不会对原图进行修改，而是返回一个新的 Image 对象

### 复制和粘贴图像到其他图像

复制图像使用 `copy()` 方法

```py
copyImage = anImage.copy()
```

- 返回一个与原图像一样的 Image 对象

---

粘贴图像使用 `paste()` 方法

```py
copyImage.paste(anotherImage, (0, 0))
```

- 在一个 Image 对象上调用 `paste()` 方法，意味着将另一个图片添加到这个 Image 对象上
- 参数
    - 粘贴源图片，即另一个图片
    - 包含 (x, y) 坐标的元组

> `copy()` 与 `paste()` 方法都不是使用系统的剪切板 

> 如果需要粘贴具有透明像素的图像，则将源图片再次作为 `paste()` 的第三个参数传入
> 第三个参数是作为遮罩层的 Image 对象
> 
> 遮罩是一个 Image 对象，其中 alpha 值有效，而 RGB 值被忽略

### 调整图片大小

调整图像大小使用 `resize()` 方法

```py
resizedImage = anImage.resize(resizeSize)
```

- 参数为一个新的宽度与高度的 tuple, 且宽度与高度的值只能是整数
- 返回一个新的 Image 对象，不会对原来的图像进行修改

### 旋转和翻转图像

旋转图像使用 `rotate()` 方法

```py
rotatedImage = anImage.rotate(angle)
```

- 参数为旋转角度
- 返回一个新的 Image 对象
- 当图像选装 90 或 270 度时，width 和 height 可能会发生变化，因为对角线控制了大小
    - 可以传入关键字参数 `expand=True` 来表明是否放大图像来适应整个旋转后的新图像

---

镜像翻转图片使用 `transpose()` 方法

```py
transposedImage = anImage.transpose(Image.FLIP_LEFT_RIGHT)

transposedImage = anImage.transpose(Image.FLIP_TOP_BOTTOM)
```

- 参数为指明是水平翻转还是垂直翻转
- 返回一个新的 Image 对象

### 更改单个像素

- 获取单个像素的颜色使用 `getpixel()` 方法
    - 参数为一个 (x, y) 坐标
- 设置单个像素的颜色使用 `putpixel()` 方法
    - 参数
        - (x, y) 坐标
        - RGBA 颜色值


