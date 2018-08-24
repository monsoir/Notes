# 在 CRA 中使用代码分割

在 Create React App 中使用代码分割，代码分割功能使用的是 [react-loadable](https://github.com/jamiebuilds/react-loadable) 第三方库

[demo 地址](https://github.com/pennyworthit/code-splitting-demo)

## 最终的效果

加载时

![](./assets/code-splitting/code-splitting-loading.png)

加载完成

![](./assets/code-splitting/code-splitting-content.png)

## 文件目录

![](./assets/code-splitting/code-structure.png)

- Loading 文件夹存放加载占位 view
- Content 文件夹存放了按需加载的组件
    - 其中的 `index.js` 文件夹中使用了 react loadable 进行按需加载

## 不使用代码分割

在 `Content/index.js` 中，直接导出 `Content` 组件

```js
import Content from './Content';
export default Content;
```

![](./assets/code-splitting/not-code-splitting.png)

从上面的结果可以看到

- 一次加载就返回了一整个 js 文件

## 使用代码分割

在 `Content/index.js` 中，通过 `react loadable` 包装 `Content` 后导出

```js
export default Loadable({
  loader: () => import('./Content'),
  loading: Loading,
});
```

![](./assets/code-splitting/do-code-splitting.png)

从上面的结果可以看到

- 加载了两次 js 文件，其中一个 js 文件中的代码暗示着独立的 `Content` 组件代码

