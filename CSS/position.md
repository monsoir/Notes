# position

决定元素定位的方式

- static
    - 默认值
    - 使用元素本身默认的布局方式进行定位，元素在页面上占据位置
    - 不能使用 `top`, `right`, `bottom`, `left`
- relative
    - 相对定位，相对元素正常的位置进行定位，元素在页面上占据位置
    - 可以再使用 `top`, `right`, `bottom`, `left` 对元素进行移动
- absolute
    - 绝对定位
    - 参照物为最近一级的父元素，且父元素的定位不为 `static`, 元素在页面上 **不占据位置**
    - 可以使用 `top`, `right`, `bottom`, `left` 对元素进行移动
- fixed
    - 绝对定位
    - 参照物为浏览器窗口，其它与 `absolute` 相同，**不占据位置**
- inherit
    - 从父元素中继承 `position` 的值


当设置 `position` 为 `absolute` 或 `fixed` 时，会发生

- 元素的 `z` 属性值增加，脱离了原来的布局流，即飘起来了，就导致了元素不再占据原本布局流中的位置，同时也会遮盖下层的元素
- 元素变为 block 元素，可以设置宽高了
- 同时元素的 `width` 也会变成 `auto`


> 正常位置：块级元素从上到下布局，内联元素从左到右进行布局，无其它位置样式影响下，元素的布局位置，假设为元素的正常位置
> 占据位置：在正常位置下，根据盒子模型占据了一定的空间大小

## References

- [CSS 最核心的几个概念](https://geekplux.com/2014/04/25/several_core_concepts_of_css.html)

