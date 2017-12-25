# 常用长度单位

## 绝对长度单位

- 物理测量单位
- 对于高 DPI 设备，`in`, `cm`, `mm` 被定义为对应的实体

### `px`

- 与显示设备相关
- 通常是一个设备像素点的显示
- 对于高分辨率屏幕，一个 CSS 像素意味着多个设备像素
- ≈ 1/96 `in`

### `pt`

- 磅(1/72 in)

### `in`

- 英寸(≈2.54cm)


## 相对长度单位

### `em`

- 表示元素的字体大小 `font-size`
- 会自动继承父级元素的值
- 不是固定大小，是倍数

> 浏览器的默认字体 `font-size` 都是 16px, 即 1em = 16px
> 为了便于计算，应使 1em = 10px
> 16px = 100% -> 10px = 62.5% -> 1em = 10px
> 因此，一般将 `<body>` 的 `font-size` 设置为 62.5%

### `rem`

- `em` 的改良版，值始终于根元素 `<html>`

## Viewport 比例长度

viewport 即文档的可视部分

### `vh`

viewport 高度的 1/100

### `vw`

viewport 宽度的 1/100

## References

- [\<length\>](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length)
- [CSS：区别 px、em、rem](https://segmentfault.com/a/1190000005936910)

