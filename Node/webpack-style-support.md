# 样式的支持

尝试添加文件 `Content.css`, 并在组件 `Content.jsx` 中引入

```css
// Content.css

.content {
  color: red;
}
```

```js
import React from 'react';
import './Content.css';

export default class Content extends React.Component {
  render() {
    return (
      <div className="content">
        I am content
        <br />
        {this.props.now}
      </div>
    );
  }
}
```

执行 `npm run dev`, 可以发现报错了

```
...
ERROR in ./src/Content.css 1:0
Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type.
> .content {
|   color: red;
| }
 @ ./src/Content.jsx 20:0-23
 @ ./src/app.jsx
 @ ./src/index.js
 @ multi (webpack)-dev-server/client?http://localhost:8080 (webpack)/hot/dev-server.js ./src/index.js
...
```

因为 Webpack 开箱时，只会支持 JavaScript 代码，其他的文件，都需要使用 loader 来进行转换或处理

## 整合 CSS

要使 Webpack 能够处理 CSS 文件，需要安装两个 loader

- [`css-loader`](https://webpack.js.org/loaders/css-loader/)
- [`style-loader`](https://webpack.js.org/loaders/style-loader/)

> 为什么要两个 loader?
> 
> [Webpack style-loader vs css-loader](https://stackoverflow.com/a/34237524/5211544)
> 
> 简单来说
> `css-loader` 实际上并不会解析 css 中的内容，它只是将 `@import`, `url()` 解析成 Webpack 能识别的 `require`, 然后会完整无缺地返回 css 文件的内容
> `style-loader` 真正将 CSS 插入到 DOM 中，使其生效，通过注入 `<style>` 标签

### 安装 loader

一般来说，`css-loader` 与 `style-loader` 总是会配合着一期使用

```sh
npm i -D style-loader css-loader
```
### 配置 Webpack

```diff
...
module: {
  rules: [
    ...
+   {
+     test: /\.css$/,
+     use: ['style-loader', 'css-loader'],
+   },
  ],
},
...
```

配置的意思时，使用正则表达式 `\.css$` 匹配文件名，遇到了 CSS 文件就使用 `style-loader` 和 `css-loader` 进行处理

实际上，这里的 loader 的写法也可以是

```js
{
  test: /\.css$/,
  use: [
    { loader: 'style-loader'},
    { loader: 'css-loader' },
  ],
},
```

注意，loader 这里出现了两个，遇到这种情况，就需要弄清楚 loader 的执行顺序

可以看到 `use` 中是一个数组，实际上，loader 的执行顺序是从数组的最后一个元素开始倒序执行，即

`style-loader(css-loader(a-css-file.css))`

[What is the loader order for webpack?](https://stackoverflow.com/questions/32234329/what-is-the-loader-order-for-webpack)

执行 `npm run dev`, 代码编译完成后，可以看到 `Content` 组件中的文字都变成了红色

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fv95p9fou6j30n4068gm7.jpg)


