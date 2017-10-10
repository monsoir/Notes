# Layers

## CALayer

常用属性

- frame
- contents
- contentGravity 位置, 值为 kCA~
- magnificationFilter 值为 kCA~
- isGeometryFlipped 上下翻转
- backgroundColor
- opacity
- isHidden
- masksToBounds
- cornerRadius
- borderWidth
- borderColor
- shadowOpacity
- shadowOffset
- shadowRadius
- shouldRasterize 默认 false, true 可以提高性能，只渲染一次，使用与不断移动，但外观整体不变的情况
- drawsAsynchronously 默认 false, true 可以提高性能，适用于经常重画的情况

## CAScrollLayer

说是说一个可以 scroll 的 layer, 但是 UIScrollView 并没有用这个来作为实现

常用属性

- scrollMode 滑动方向
- scroll(to:) 手动控制滑动位置

选择使用/不使用的参考条件

- 需要轻量级的滑动控制，并只需要手动（代码）控制滑动
- 如果想让用户能够滑动组件，最后还是用回 UIScrollView
- 如果要滚动一张很大，很大那种图片，考虑使用 CATiledLayer

## CATextLayer

快速渲染 plain text 或 attributed strings

CATextLayer 中的文字字体值类型是 `CTFontRef` 或 `CGFontRef`

常用属性

- string
- font `CTFontCreateWithName`
- foregroundColor
- isWrapped
- alignmentMode 值为 kCA~
- contentsScale 一般为 `UIScreen.main.scale`

对于自己创建的 `CALayer`, 即所有的 layer 类, 需要显式设置 `contentsScale`, 否则可能会在 retina 显示屏上得到像素级的显示效果

## AVPlayerLayer

## CAGradientLayer

常用属性

- startPoint
    - 开始位置，值类型为 `CGPoint`, 其中的 `x`, `y` 的值为 0~1
    - 指的是相对位置，即 x=0 为 `CAGradientLayer` 的最左侧，y=1 为 `CAGradientLayer` 的最底部
- endPoint
    - 同上
- type 唯一值 `kCAGradientLayerAxial`
- colors 一个 `CGColor` 数组，用来存储渐变用的颜色
- locations 
    - 一个数字的数组，其中的值为 0~1, 当到达了其中的一个值时，颜色渐变结束，并使用下一个颜色
    - locations.length 需等于 colors.length

## CAReplicatorLayer

常用属性

- instanceCount 重复个数
- instanceDelay 每个重复实例之间的时间，以秒为单位
- preservesDepth true 则有 3D 效果，false 就 2D 效果
- instanceColor 每个重复实例的基础颜色
- 在 instanceColor 基础上调整 RGBA
    - instanceRedOffset
    - instanceGreenOffset
    - instanceBlueOffset
    - instanceAlphaOffset
- instanceTransform 通过这个来控制重复的形式，旋转？平移？

## CATiledLayer

异步加载，逐部分加载内容，适合展现大图片，或对大图片每次只查看其中一小部分的情况，节省内存

使用 `CATiledLayer` 常用的一个方法是继承 `UIView` 并覆盖 `layerClass` 类方法，返回 `CATiledLayer.self`

常用属性

- contentsScale
- tileSize
- levelsOfDetail 默认为 1
- levelsOfDetailBias 默认为 0, 放大时有用

## CAShapeLayer

常用属性

- path 通过 `UIBezierPath` 创建后再获取 CGPath
- fillColor
- fillRule 值为 kCA~
- lineCap
- lineDashPattern
- lineDashPhase
- lineJoin
- lineWidth
- miterLimit
- strokeColor

## CATransformLayer

用于展现 3D 结构

## CAEmitterLayer

这个真有点炫，设计了两个类

- CAEmitterLayer
- CAEmitterCell

CAEmitterlayer 是用来渲染多个 CAEmitterCell 来组成炫酷的效果

## References

- [https://www.raywenderlich.com/169004/calayer-tutorial-ios-getting-started](https://www.raywenderlich.com/169004/calayer-tutorial-ios-getting-started)

