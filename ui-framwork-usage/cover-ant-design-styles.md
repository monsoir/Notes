# 覆盖 Ant Design 样式

- 使用 Less 样式文件
- 使用全局形式覆盖
- 由于使用全局形式的覆盖，因此需要在对覆盖的样式使用另一层样式进行包裹，避免影响不需要覆盖的组件样式
- 通过 `className` 进行样式赋值

例如，在 Ant Design 中，`Tree` 组件中的每一个节点的大小时随文本大小的决定的，可以通过重写宽度的样式，实现对每个节点的宽度修改

```less
.customLi { // 避免全局污染，使用自定义的一层进行包裹
  :global { // 覆盖的样式需要放到全局中
    .ant-tree-node-content-wrapper { // Ant Design 中的样式
      width: 98%; // 需要覆盖的属性
    }
  }
}
```

## References

- [覆盖组件样式](http://03x.pro.ant.design/docs/style#覆盖组件样式)



