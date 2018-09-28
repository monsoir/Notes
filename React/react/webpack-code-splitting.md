# Webpack 代码分割

从 Webpack v4 开始，原来用来进行代码分割的插件 `CommonsChunkPlugin` 已经被弃用，而使用 [SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/) 取代

> CommonsChunkPlugin 我也不懂，直接就从 SplitChunksPlugin 开始了

## 不进行分割

我们原有的 `webpack.config.dev.js` 配置中，只定义了一个入口

```js
...
module.exports = {
  entry: path.join(rootPath, 'src/index.js'),
  ...
};
...
```

执行 `npm run build`, 可以看到控制台的输出

```
Hash: 08ad500b6b32afb44d30
Version: webpack 4.17.2
Time: 1654ms
Built at: 09/12/2018 3:30:30 PM
                       Asset       Size  Chunks             Chunk Names
main.08ad500b6b32afb44d30.js    716 KiB    main  [emitted]  main
                  index.html  306 bytes          [emitted]
Entrypoint main = main.08ad500b6b32afb44d30.js
[./src/app.jsx] 2.57 KiB {main} [built]
[./src/index.js] 153 bytes {main} [built]
    + 21 hidden modules
Child html-webpack-plugin for "index.html":
     1 asset
    Entrypoint undefined = index.html
    [./node_modules/html-webpack-plugin/lib/loader.js!./public/index.html] 437 bytes {0} [built]
    [./node_modules/webpack/buildin/global.js] (webpack)/buildin/global.js 509 bytes {0} [built]
    [./node_modules/webpack/buildin/module.js] (webpack)/buildin/module.js 519 bytes {0} [built]
        + 1 hidden module
```

可以看到 JS 文件 `main.08ad500b6b32afb44d30.js` 有 700+ KB 的大小，而这当中包含了：

- 业务代码，即：app.jsx
- React 的代码，react, react-dom

我们也可以通过浏览器的监控看到，整个页面的请求只有一个

![](https://ws4.sinaimg.cn/large/0069RVTdgy1fv6upnzxtaj30ts0vqgnx.jpg)

## Webpack 对代码分割的操作

- 默认情况下，开箱即用的 `SplitChunksPlugin` 适用于大多数情况下
- Webpack 在以下的情况中，会自动将代码分割
    - 新的代码块可以共享，或者从 `node_modules` 中加载的代码
    - 新的代码块大小(在 min+gz 之前的大小) > 30kb
    - 最大并行网络请求数(请求代码块) <= 5
    - 首页的最大并行网络请求数 <= 3

## 代码分割第一步

配置入口选项

```diff
...
- entry: path.join(rootPath, 'src/index.js'),
+ entry: {
+   app: path.join(rootPath, 'src/index.js'),
+   react: ['react', 'react-dom'],
+ },
...
```

修改 entry 值为一个对象，即业务代码打包成 `app` 代码块，React 相关的库，打包成 `react` 代码块

执行 `npm run build`, 控制台的输出为

```
Hash: 106ac7643eeeaa8fc19c
Version: webpack 4.17.2
Time: 2042ms
Built at: 09/12/2018 4:19:04 PM
                        Asset       Size  Chunks             Chunk Names
  app.106ac7643eeeaa8fc19c.js    716 KiB     app  [emitted]  app
react.106ac7643eeeaa8fc19c.js    712 KiB   react  [emitted]  react
                   index.html  381 bytes          [emitted]
Entrypoint app = app.106ac7643eeeaa8fc19c.js
Entrypoint react = react.106ac7643eeeaa8fc19c.js
[./src/app.jsx] 2.57 KiB {app} [built]
[./src/index.js] 153 bytes {app} [built]
[0] multi react react-dom 40 bytes {react} [built]
    + 21 hidden modules
Child html-webpack-plugin for "index.html":
     1 asset
    Entrypoint undefined = index.html
    [./node_modules/html-webpack-plugin/lib/loader.js!./public/index.html] 437 bytes {0} [built]
    [./node_modules/webpack/buildin/global.js] (webpack)/buildin/global.js 509 bytes {0} [built]
    [./node_modules/webpack/buildin/module.js] (webpack)/buildin/module.js 519 bytes {0} [built]
        + 1 hidden module
```

可以看到，打包后得到的代码块是 `app.106ac7643eeeaa8fc19c.js`, `react.106ac7643eeeaa8fc19c.js`

但可以看到，`app` 代码块还是与 `react` 代码块的大小差不多，都是 700+ kb, `react` 部分的代码块并没有从 `app` 中分离出来，还是有重复的部分

同时，我们也可以从浏览器的监控中看到，页面请求了两个 JS 文件，因为 index.html 中插入了两个 `<script>`

```html
<script type="text/javascript" src="app.106ac7643eeeaa8fc19c.js"></script><script type="text/javascript" src="react.106ac7643eeeaa8fc19c.js"></script></body>
```

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fv6v137hbhj30ww0v8whe.jpg)

下面，就需要将重复的代码抽离出来，避免可以模块化的代码进行了重复的请求

## 代码分割第二步

关于代码分割的配置项，位于

```js
optimization: {
  splitChunks: {
    ...
  },
},
```

修改一下 `webpack.config.dev.js` 文件

```diff
module.exports = {
  ...
  optimization: {
    splitChunks: {
      chunks: 'all', 
    },
  },
};
```

执行 `npm run build`, 控制台中得到以下输出

```
Hash: 15bb4e959185399a4f63
Version: webpack 4.17.2
Time: 1774ms
Built at: 09/12/2018 4:28:40 PM
                                    Asset       Size             Chunks             Chunk Names
              app.15bb4e959185399a4f63.js   10.7 KiB                app  [emitted]  app
            react.15bb4e959185399a4f63.js   6.58 KiB              react  [emitted]  react
vendors~app~react.15bb4e959185399a4f63.js    708 KiB  vendors~app~react  [emitted]  vendors~app~react
                               index.html  469 bytes                     [emitted]
Entrypoint app = vendors~app~react.15bb4e959185399a4f63.js app.15bb4e959185399a4f63.js
Entrypoint react = vendors~app~react.15bb4e959185399a4f63.js react.15bb4e959185399a4f63.js
[./src/app.jsx] 2.57 KiB {app} [built]
[./src/index.js] 153 bytes {app} [built]
[0] multi react react-dom 40 bytes {react} [built]
    + 21 hidden modules
Child html-webpack-plugin for "index.html":
     1 asset
    Entrypoint undefined = index.html
    [./node_modules/html-webpack-plugin/lib/loader.js!./public/index.html] 437 bytes {0} [built]
    [./node_modules/webpack/buildin/global.js] (webpack)/buildin/global.js 509 bytes {0} [built]
    [./node_modules/webpack/buildin/module.js] (webpack)/buildin/module.js 519 bytes {0} [built]
        + 1 hidden module
```

可以看到，打包后得到的代码块有 3 个

- `app.15bb4e959185399a4f63.js` 10.+kb
- `react.15bb4e959185399a4f63.js` 6.+kb
- `vendors~app~react.15bb4e959185399a4f63.js` 700+kb
    - `a~b` 符号表示 a,b 模块公用的代码块

我们可以从浏览器的监控中看到，页面请求了 3 个 JS 文件

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fv6v54yj3vj30yi0v8n0m.jpg)

### 关于 `chunks` 的选项

对于 `chunks` 属性，其取值可以为字符串或一个函数

当 `chunks` 为字符串时，取值有 `all`, `async`, `initial`

- `initial` 只针对静态引入的代码进行优化
- `async` 只针对动态引入的代码进行优化，即使用 `import()` 等技术进行延迟加载的代码
- `all` 覆盖以上两种情况，是最全面的

当 `chunks` 为函数时，函数签名为

```js
chunks(chunk) {
  // 针对 chunk, 即每一模块，进行判断，返回 true/false 指明是否需要优化
  return aBoolean;
}
```

## 实际上

从 Webpack v4 开始，Webpack 会自动根据上文提到过的默认条件进行代码分割，而且是开发者零配置的代码分割

而上面说的这些代码分割的步骤，是针对项目庞大起来后需要进行自定义的代码分割

> 比如：现在有模块 a, b, c
> 
> 若使用 Webpack 默认的代码分割方法，是会将 a, b, c 分别打包成块。但现在不想这样，我们需要将 a,b 打成一个块，c 自己一个块，那此时，我们就需要通过重写 `optimization.splitChunks.cacheGroups` 来进行控制了

## References

- [Code Splitting Webpack 对代码分割的介绍](https://webpack.js.org/guides/code-splitting/)
- [Webpack 使用具体例子进行代码分割的分析](https://github.com/webpack/webpack/blob/master/examples/many-pages/README.md)
- [Webpack 4 配置最佳实践](https://zxc0328.github.io/2018/06/19/webpack-4-config-best-practices/)

