# CSSOM API

显示相关的 API, 分为三大部分

- 窗口
- 滚动
- 布局

## 窗口

都挂靠在 `window` 对象上

- moveTo(x, y) 将窗口移动到屏幕的特定位置
- moveBy(x, y) 将窗口移动特定的距离
- resizeTo(x, y) 改变窗口到特定大小
- resizeBy(x, y) 以特定大小改变窗口尺寸

> 实际上，以上 API 可能会由于浏览器的安全考虑而禁用或没有实现，执行后并没有什么效果

- window.open 打开新的窗口

```js
window.open("about:blank", "_blank", "some feature");
```

## 滚动

滚动相关的 API 又可以分为两种

- 视口(可视区域)滚动 API
- 元素滚动 API

### 视口滚动

属性或方法挂靠在 `window` 对象上

- 属性
    - scrollX / pageXOffset 当前在 X 方向上的滚动距离
    - scrollY / pageYOffset 当前在 Y 方向上的滚动距离

- 方法
    - scroll(x, y) / scrollTo(x, y) 使页面滚动到特定位置，描述的是最终结果
    - scrollBy(x, y) 使页面滚动特定距离，描述的是滚动过程

监听视口滚动事件

```js
document.addEventListener("scroll", (event) => {
  // do something...
});
```

### 元素滚动

属性或方法挂靠在元素上

- 属性
    - scrollTop 当前在 X 方向上的滚动距离
    - scrollLeft 当前在 Y 方向上的滚动距离
    - scrollWidth 元素内部滚动内容的宽度，即 iOS 中 UIScrollView 的 contentSize.width
    - scrollHeight 元素内部滚动内容的高度，即 iOS 中 UIScrollView 的 contentSize.height

- 方法
    - scroll(x, y) / scrollTo(top. left) 使元素滚动到特定的位置
    - scrollBy(x, y) 是元素滚动特定距离
    - scrollIntoView(arg) 滚动元素所在的父元素，使得元素滚动到可视区域，通过 arg 来指定滚动到的位置（中间、开始、就近）

监听元素的滚动事件

```js
aElement.addEventListener("scroll", (event) => {
  // do something...
});
```

## 布局

布局相关的 API 又可以分为两种

- 全局的布局信息，即窗口
- 元素的布局信息

### 全局布局信息

挂靠在 `window` 对象上

- innerHeight, innerWidth 视口的大小
- outerWidth, outerHeight 浏览器窗口的大小
- devicePixelRatio 物理像素与 CSS 像素单位的倍率关系
    - Retina 屏是 2
- screen 屏幕尺寸相关信息
    - screen.width, screen.height 设备屏幕尺寸
    - screen.availWidth, screen.availHeight 设备屏幕可渲染区域的尺寸
        - 部分 Anroid 设备会将屏幕可视的一部分用作虚拟按键
    - screen.colorDepth, screen.pixelDepth 值固定为 24

### 元素布局信息

> 如何获取元素的宽高此类的尺寸信息？
>
> 元素实际上是安置在「盒子」中，因此，所谓的元素宽高，应该是指这个「盒子」的宽高

- getClientRects
    - 返回一个数组，每个元素代表其中的每个「盒子」占据的客户端矩形区域
    - 每个矩形区域可以使用 `x`, `y`, `width`, `height` 来获取位置和尺寸
- getBoundingClientRect
    - 返回元素所在「盒子」的矩形区域
    - 该区域会包含当 `overflow` 为 `visible` 时的子元素区域

**以上两个 API 获取到的矩形区域都是相对于视口坐标**，由于视口是固定不变的，因此每次滚动后，以上两个 API 获取到的值都会发生变化（相对于视口坐标，滚动后就变化了）。因此，当想要获得某个元素相对于整个页面的位置，需要通过 JavaScript 计算出来

```js
const offsetY = Math.abs(document.documentElement.getBoundingClientRect().y) + Math.abs(anElement.getBoundingClientRect().y)
```




