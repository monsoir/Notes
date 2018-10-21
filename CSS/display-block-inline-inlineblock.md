# display 的 block, inline, inline-block

## block

块级元素 block level element

block 元素通常是被视为独立的一块，单独占一行

常见默认 block 元素

- div
- form
- table
- p
- pre
- h1...
- dl
- ol
- ul
- center

## inline

内联元素 inline element

inline 元素前后不换行，都在一行中显示，除非一行占满了

常见默认 inline 元素

- span
- a
- strong
- em
- label
- input
- select
- textarea
- img
- br

## block 与 inline 元素的关系

- block 元素与 inline 元素可以通过修改 `display` 属性来进行互换
- block 元素可以设置 `width`, `height` 属性，inline 元素无效
- block 元素默认撑满父元素，inline 元素根据自身内容、子元素决定宽度

## inline-block

元素也可以设置为

```css
display: inline-block;
```

这样，此元素对外呈现 inline 样式，对内呈现 block 样式，即对外可以与同级元素共处一行，对内可以设置元素宽高

