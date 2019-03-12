# CSS 居中

## 水平居中

### [行内元素](./html-element.md)

设置块级父元素 `text-align` 为 `center`

<p class="codepen" data-height="" data-theme-id="" data-default-tab="css,result" data-user="monsoir" data-slug-hash="RdLMbj" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="inline-element-align-center">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/RdLMbj/">
  inline-element-align-center</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### [块级元素](./html-element.md)

1. 设置该块级元素 `margin-left`, `margin-right` 为 `auto`, 原理：
    - 当 `margin` 不为具体的数字时，是根据父元素的宽度进行计算的
    - 当 `margin-left` 设置为 `auto` 时，左外边距为元素占据父元素后剩下的长度
    - `margin-right` 同理于 `margin-left`
    - 当 `margin-left` 与 `margin-right` 都为 `auto` 时，左右外边距就会平均分父元素剩下的长度，达到水平居中的效果
2. 为该块级元素设置一个固定宽度，否则不起效

<p class="codepen" data-height="" data-theme-id="" data-default-tab="css,result" data-user="monsoir" data-slug-hash="MxEVQK" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="MxEVQK">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/MxEVQK/">
  MxEVQK</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## 垂直居中

### [行内元素](./html-element.md)

#### 单行

##### 方法一

确定文本不超过一行的，设置元素的上下内边距(`padding-top`, `padding-bottom`)为相同值

下面例子垂直居中的目标是红色区域的文字

<p class="codepen" data-height="" data-theme-id="" data-default-tab="css,result" data-user="monsoir" data-slug-hash="ZPXoze" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="ZPXoze">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/ZPXoze/">
  ZPXoze</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

<br />
> 父元素设置了 `padding` 是为了防止子元素由于设置了 `padding` 而导致了部分超出范围不可视的问题

##### 方法二

当方法一的方式方式不起作用时，可以将设置 `line-height` 与 `height` 相等，但需要保证文本不超过一行

下面例子垂直居中的目标是红色区域的文字

<p class="codepen" data-height="" data-theme-id="" data-default-tab="css,result" data-user="monsoir" data-slug-hash="YgrvbQ" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="block-element-align-center-vertically-2">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/YgrvbQ/">
  block-element-align-center-vertically-2</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

#### 多行

##### 方法一

同样可以设置元素的上下内边距(`padding-top`, `padding-bottom`)为相同值

<p class="codepen" data-height="" data-theme-id="" data-default-tab="css,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

##### 方法二

使用 `<table>`, 其单元格默认垂直居中

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

##### 方法三

构建类似 `<table>` 的布局

1. 父元素设置 `display` 为 `table`
2. 目标元素设置 `display` 为 `table-cell`
3. 目标元素还需要设置 `vertical-align` 为 `middle`, 这是与直接设置 `<table>` 标签不同的地方

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

##### 方法四

使用 Flexbox 布局

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

##### 方法五

使用伪元素，又称幽灵元素

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>


### [块级元素](./html-element.md)

#### 已知元素高度

思路在于

1. 将目标元素的起点已到父元素高度的一半
    - 当元素 `position` 设置为 `absolute` 时，`top` 设置为百分比时，计算的参照对象为父元素
2. 再通过移动目标元素的外边距来确定位置，移动的距离为目标元素高度的一半（因此需要知道目标元素的高度）

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

#### 未知元素高度

思路与已知高度时大致相同

1. 将目标元素的起点已到父元素高度的一半
2. 再通过使用 CSS 函数 `translateY` 移动目标元素的垂直位置
    - `translate` 函数中的参数若为百分比，则计算的参照物为元素自身的大小

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

#### 使用 Flexbox

使用 Flexbox 进行垂直居中布局非常简单，甚至只需要设置父元素的样式即可

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## 水平且垂直居中

### 已知宽高

实现思路

由于已知宽高，可以使用绝对定位将目标元素在父元素范围内，将 `top` 与 `left` 便宜 50%, 再使用具体数值的负外边距，`margin-left`, `margin-top` 来调整位置

> 若目标元素含有内边距，则还需要将内边距纳入调整距离的计算

下面例子，居中的元素为粉红色区域，上面的文字只是告知这个区域含有内边距

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 未知宽高

#### 方法一

与「已知宽高」的方法大致相同，但调整距离的方式是使用 `translate` 方法

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

#### 方法二

是最简单的方法，即使用 Flexbox 布局

只需要设置三个 CSS 属性

```css
display: flex;
justify-content: center;
align-items: center;
```

<p class="codepen" data-height="" data-theme-id="" data-default-tab="html,result" data-user="monsoir" data-slug-hash="rRGreO" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="rRGreO">
  <span>See the Pen <a href="https://codepen.io/monsoir/pen/rRGreO/">
  rRGreO</a> by Wen Yongyang (<a href="https://codepen.io/monsoir">@monsoir</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## References

- [CSS居中完整指南](https://www.w3cplus.com/css/centering-css-complete-guide.html)

