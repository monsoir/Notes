# 圣杯布局的实现

最后的效果是

![](https://ws1.sinaimg.cn/large/006tNc79gy1fz3r036hkwj30ir09a3yr.jpg)

实现代码 [gist](https://gist.github.com/Monsoir/d62d4e3322f3bc00810c04e4d9d45e58)

## 基本的设置

### 定义好 HTML 骨架

HTML 代码

```html
<header>header</header>
<div>
  <div class="center">center</div>
  <div class="left">left</div>
  <div class="right">right</div>
</div>
<footer>footer</footer>
```

基本的 CSS 代码，定义一下文字的排版和各区域的颜色，方便识别

```css
header {
  text-align: center;
  background-color: red;
}

footer {
  text-align: center;
  background-color: yellow;
}

div {
  text-align: center;
}

.center {
  background-color: orange;
}

.left {
  background-color: silver;
}

.right {
  background-color: pink;
}
```

- 使用语义化的标签定义布局的头部和底部，`<header>`, `<footer>`
- 中间内容区域，使用三个 `<section>` 进行定义，首先定义类选择器为 `center` 的区域，这个也是主要内容的区域，而 `left` 和 `right` 两个 `<section>`, 都将会围绕 `center` 进行布局

效果

![](https://ws1.sinaimg.cn/large/006tNc79gy1fz3qzkp724j30f9035mx2.jpg)

## 调整内容区域

### 浮动元素

可以看到，现在内容区域的三部分，由于 `div` 本身为块级元素，所以，`center`, `left`, `right` 会各占一行

接下来的重点是，如何将内容区域，即 `center`, `left`, `right` 排列在同一行

要将三者排列在同一行，可以使用 `float: left` 进行控制，将 `div` 设置成浮动

> 将 div 设置成浮动，那么 div 就会尽可能向左上方或右上方进行逼近（由浮动值 left, right 进行控制），行为像水中的气泡一样，不断向上提升
> 但缺点是浮动的元素会造成其父元素高度塌陷，即浮动元素的高度并不能撑开父元素

于是，CSS 修改成如下，添加上浮动

```css
.center {
  float: left;
  background-color: orange;
}

.left {
  float: left;
  background-color: silver;
}

.right {
  float: left;
  background-color: pink;
}
```

效果

![](https://ws1.sinaimg.cn/large/006tNc79gy1fz3rv9eqzyj30fi01hglh.jpg)

### 清除浮动

由于将内容区域的三个元素都设置成了浮动，因此，`<footer>` 元素将围绕着它们进行布局，而这种结果，也正是浮动的设计初衷

> 浮动的设计原因，让其他内容围绕着浮动元素进行布局，而浮动的缺点是
> - 造成其父元素高度塌陷
> - 影响其兄弟元素的正常排列布局

然而，`<footer>` 围绕着内容区域布局，这里直接导致的后果是，`<footer>` 排在了内容区域的后面，而不是始终放在最下面。要让 `<footer>` 始终放在最下面，需要清除浮动

清除浮动由两种方法

#### 使用 BFC 进行包裹

BFC 即 Block Fromatting Context, 块级格式上下文

简单来说，就是创建一个父元素，用它来包裹浮动的元素，而这个父元素，其 CSS 属性至少为

```css
.cleaer-fix {
  overflow: hidden; /* hidden 不是唯一，只要不是 visible 就行 */
}
```

这样，就可以清除浮动元素造成的高度塌陷

#### 使用 clear 属性

```css
.clear-fix {
  clear: both;
}
```

这种做法，也可以清除浮动，清除浮动的原理是为元素添加上外边距，使得元素能够出现在浮动元素的下方

---

而其原理也是这种做法的缺点，因为这会隐式地为元素添加上外边距，可能会影响到元素自身的样式（主要是 `padding-top`），因此，一般会额外添加一个空元素来清除浮动

```html
<p class="clear-fix"></p>
```

---

而这种做法，如果页面上存在多个需要清除浮动的地方，将会有很多空元素，造成浪费，因此，也可以使用伪元素选择器来创建元素，达到清除浮动的效果，如果将伪元素放在内容区域，则是

```css
/* 多添加一个 container 类 */
.container::after {
  content: '';
  clear: both;
  visibility: hidden;
  display: block;
  font-size: 0;
  height: 0;
}
```

这部分的代码

```html
<header>header</header>
<div class="container">
  <div class="center">center</div>
  <div class="left">left</div>
  <div class="right">right</div>
</div>
<footer>footer</footer>
```

```css
/* ... */

.container::after {
  content: '';
  clear: both;
  visibility: hidden;
  display: block;
  font-size: 0;
  height: 0;
}

/* ... */
```

效果

![](https://ws2.sinaimg.cn/large/006tNc79gy1fz3tccze6yj30ff01zt8l.jpg)


## 将 center 部分居中

现在的 center 部分是处于内容区域的第一个，我们需要将它夹在 left 和 right 中间

我们将修改一下两个类的 css

```css
.container {
  padding: 0 100px;
}

.center {
  width: 100%;
  float: left;
  background-color: orange;
}
```

- 这里为 container 添加了 `padding`, 指定了其 `padding-left` 与 `padding-right` 为 100px, 即 container 的子元素，与 containere 的左右间隔为 100px
- 同时将 center 的宽度设置为 100%, 即撑满整个 container 的内容区域
- 需要注意的是，一个元素的宽度，值是元素内容区域的宽度，并不包含其内边距及以外的部分

得到的效果是

![](https://ws3.sinaimg.cn/large/006tNc79gy1fz3tmllargj30fg02e3ye.jpg)

> 奇怪，为什么 left 会跟 center 对齐，而不是在它的行最前面？
> 
> center 是浮动元素，它已经把 container 的一行占满了，所以 left 会排在第二行；还要记得，刚才把 container 的内边距设置为 100px, 所以其子元素的左右边距，都要距离父元素至少 100px

## 调整 left 位置

现在 left 是处在第二行，我们的目标是需要 left 跟 center 处在同一行，并且在 center 的前面

这里需要对 left 进行精确移动，就需要用到相对定位，相对定位，意思就是相对原来的位置进行定位，记住这一点很重要，需要记清楚现在 left 的位置

### 上移 left

修改 left 的样式为

```css
.left {
  float: left;
  margin-left: -100%;
  
  background-color: silver;
}
```

- 上移 left, 实际上只需要将 left 的 `margin-left` 设置为 `-100%`
- 当 `margin-left` 的值设置为百分数时，其参考的值为父元素的内容宽度

> 为什么这里设置 margin-left 就可以将 left 上移？
> 
> 需要弄清楚的是，left 和后面的 right, 原来的时候，之所以会排在 center 的下面，是因为 center 占满了第一行，于是 left 才会被迫换到第二行，实际上，center, left, right 在某种意义上来说，都是处在同一行
> 因此，当 left 的 margin-left 设置为 -100% 时，参考父元素的宽度后计算得出，left 的左侧外边距为负的父元素宽度的距离，就会将 left 移到了父元素的最前端，最后的结果就是 left 和 center 排在了同一行

效果

![](https://ws3.sinaimg.cn/large/006tNc79gy1fz3unwrmpnj30gu02ia9y.jpg)

### 将 left 移到 center 的前面

修改 left 的样式为

```css
.left {
  float: left;
  margin-left: -100%;
  position: relative;
  right: 100px;
  
  background-color: silver;
}
```

- 这里我们需要对 left 进行比较精准的移动，需要用到相对布局，因此设置 `position: relative`, 这为使用 `right` 进行移动提供了可行性
- `right: 100px` 将 left 向水平向左移动，其右侧距离原来位置 100px (这个 100px 是之前设置 container 左右边距得出的)

实际上，个人感觉这个 `right` 是挺有歧义的，至少从字面上，我可以瞎猜两种意思

- 向右移动 ?px
- 元素的右边距离父元素最左边 ?px

因此，需要根据其定义来进行操作

> When position is set to relative, the right property specifies the distance the element's right edge is moved to the left from its normal position.
> 
> 当 position 设置为 relative 时，right 属性表示：以右侧为准，将元素从其正常位置向左移动 xx 距离
> 即元素移动后的结果是，元素的右侧，距离其原来位置的右侧 xx 远

效果

![](https://ws1.sinaimg.cn/large/006tNc79gy1fz3uyqfn11j30gx0283ye.jpg)

最后，我们在设置一下 left 的宽度

```css
.left {
  float: left;
  margin-left: -100%;
  position: relative;
  right: 100px;
  width: 100px;
  
  background-color: silver;
}
```

效果

![](https://ws3.sinaimg.cn/large/006tNc79gy1fz3uztyjhxj30gv02e746.jpg)


### 调整 right 位置

#### 上移 right

要上移 right, 其思路跟上移 left 是差不多的，right 在某种意义上来说，其实也是跟 center, left 处于同一行，因此，只要调整间距就可以达到上移 right 的目标

修改 right 的 CSS

```css
.right {
  float: left;
  margin-right: -100px;

  background-color: pink;
}
```

- 调整 `margin-right`, 这里使用了绝对数字，-100px 是根据之前 container 设置的 padding 得出的

再调整一下 right 的宽度

```css
.right {
float: left;            
  margin-right: -100px;
  width: 100px;
  background-color: pink;
}
```


效果

![](https://ws3.sinaimg.cn/large/006tNc79gy1fz3wvv3gigj30gu01zwed.jpg)

最后在设置一下各部分的高度，即可得出

![](https://ws3.sinaimg.cn/large/006tNc79gy1fz3wyb3ro8j30gx09c3yi.jpg)

## 小结

- CSS 代码编写时，很静态，实际上是很动态的
- 虽然 CSS 属性的编写顺序不影响最后的结果，但每个属性的编写顺序，是可以体现出动态的过程

