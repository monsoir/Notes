# 关于 Tree 组件的 TreeNode 递归生成

在官方的 Demo 中，提供了一个递归遍历生成子节点的函数

```jsx
const loop = data => data.map((item) => {
  if (item.children && item.children.length) {
    return <TreeNode key={item.key} title={item.title}>{loop(item.children)}</TreeNode>;
  }
  return <TreeNode key={item.key} title={item.title} />;
});
```

写递归程序的时候，总会不知所措，但看到了 [理解递归](https://acl.readthedocs.io/en/latest/zhCN/ch3-cn.html#understanding-recursion)

> 学生在学习递归时，有时候是被鼓励在纸上追踪 (trace)递归程序调用 (invocation)的过程。 (288页「译注：附录 A 追踪与回溯」可以看到一个递归函数的追踪过程。)但这种练习可能会误导你：一个程序员在定义一个递归函数时，通常不会特别地去想函数的调用顺序所导致的结果。
>
> 如果一个人总是需要这样子思考程序，递归会是艰难的、没有帮助的。递归的优点是它精确地让我们更抽象地来设计算法。你不需要考虑真正函数时所有的调用过程，就可以判断一个递归函数是否是正确的。
>
> 要知道一个递归函数是否做它该做的事，你只需要问，它包含了所有的情况吗？

若按照上面的描述进行理解的话

1. `loop` 函数的工作是返回一颗树
2. `loop` 函数的回调函数应该是返回一个树节点

    ```jsx
    const loop = data => data.map((item) => {
      return <TreeNode key={item.key} title={item.title} />;
    });
    ```

3. 不对，要是当前节点还有子节点，那还要继续渲染它的子节点，那可以将子节点的数据当作参数，继续调用 `loop` 函数

    ```jsx
    const loop = data => data.map((item) => {
      if (item.children && item.children.length) {
        return <TreeNode key={item.key} title={item.title}>{loop(item.children)}</TreeNode>;
      }
      return <TreeNode key={item.key} title={item.title} />;
    });
    ```

4. 最后，会发现，这算法类似二叉树的前序遍历(根左右，先渲染了根节点，再不断渲染子节点)

