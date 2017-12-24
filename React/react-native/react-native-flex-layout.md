# React native flex 布局

[TOC]

## 属性

### flex

> is a positive number, it makes the component flexible and it will be sized proportional to its flex value

- 与 CSS 的表现有所差别，只能为整数值，不带单位的那种
- 当 flex 为正整数时，组件将会根据 flex 的值调整组件的大小
	- flex = 0: 组件根据 width 和 height 调整大小，并且不可调整
	- flex = -1: 组件根据 width 和 height 调整大小。当位置不够时，组件将会根据 minWidth 和 minHeight 调整
	- flex = 2: 占用当 flex = 1 时的两倍空间

### flexDirection

> controls which directions children of a container go

子视图在容器中排布的方向，用来确定主轴

```
enum {
	'row', // 横排正序 leading to trailing
	'row-reverse', // 横排逆序 trailing to leading
	'column', // 竖排正序 top to bottom
	'column-reverse', // 竖排逆序 bottom to top
}
```

### justifyContent

> aligns children in the main direction

子视图在主轴的排列方式

```
enum {
	'flex-start', // 从开始位置排列，从左到右，从上到下
	'flex-end', // 从结尾位置排列
	'center', // 居中
	'space-between', // 空间在子视图之间，子视图等间距
	'space-around', // 空间环绕子视图，每个子视图占据相同的空白，与 space-between 类似，但包括开始与结尾部分的空白
}
```

### alignItems

> defines how the browser distributes space between and around flex items along the cross-axis of their container

决定子视图在次轴方向的排列方式（次轴方向设置在父视图上）

```
enum {
	'flex-start', // 从开始位置排列
	'flex-end', // 从结尾位置排列
	'center', // 居中
	'stretch', // 拉伸
}
```

### alignSelf

> aligns flex items of the current flex line overriding the align-items value. If any of the item's cross-axis margin is set to auto, then align-self is ignored

决定元素在父视图次轴方向的排列方式（设置在子视图上），其值会覆盖父视图的 alignItems 的值。**默认值为 auto**，当值为 auto 时， align-self 的值将会被忽略

即：主要用来重写父视图的 alignItems

值与 alignItems 一样


### position

布局方式，默认是 relative

> An item marked with Position = Absolute is positioned absolutely in regards to its parent

一个被标记为 postition = absolute 的视图，只是相对父视图的 absolute

```
enum {
	'absolute', // 能提供更精准的控制
	'relative',
}
```

设置 position = absolute 后，可通过以下属性设置位置

```
Left
Top
Right
Bottom
Start
End
```

e.g `position=absolute;start=0;Top=0;Width=0;Height=0`

### flexWrap

> controls whether children can wrap around after they hit the end of a flex container

```
enum {
	'wrap', // 子视图超过一行的长度后将会换行
	'nowrap', // 子视图超过一行的长度后不换行
}
```



