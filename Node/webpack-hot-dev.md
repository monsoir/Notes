# 开发时热更新

目前为止，当修改了代码需要预览结果时，是需要重新刷新浏览器或者打开 `index.html` 文件，这样是效率比较低的做法

我们的目标是，当我们修改了文件保存后，Webpack 能够自动编译，并通知浏览器重新加载页面

实现检测文件并自动重新编译的方法，Webpack 提供了 3 种方法

- webpack's Watch Mode 通常是结合命令行使用，`webpack --watch`
- webpack-dev-server 最经常使用
- webpack-dev-middleware 通常是使用创建自定义的文件服务器时使用，例如，结合 Express 使用，实际上 webpack-dev-server 内部也是使用 webpack-dev-middleware 实现

## 创建服务器

由于需要通过浏览器访问，并且需要浏览器能够自动刷新页面，即能够主动向浏览器发送消息，通知浏览器重新加载页面，这需要通过 WebSocket 实现双向通信。所以，我们需要一个服务端

### webpack-dev-server 

[webpack-dev-server](https://webpack.js.org/guides/development/#using-webpack-dev-server) 能够提供一个简单的服务器，并提供 live loading 的功能


安装 webpack-dev-server

```sh
npm i -D webpack-dev-server
```

配置 `webpack.config.dev.js`

```diff
module.exports = {
  ...
+ devServer: {
+   port: 8080, // 服务端口
+   compress: true, // 是否使用 gzip 进行压缩
+   historyApiFallback: true, // 当访问出现 404 时，返回 index.html
+   noInfo: false, // 是否隐藏编译信息
+   open: true, // 是否自动打开浏览器
+ },
};
```

关于 devServer 的更多配置选项 [👉 DevServer](https://webpack.js.org/configuration/dev-server/)

修改 npm scripts

```diff
"scripts": {
+ "dev": "webpack-dev-server --inline --progress --config config/webpack.config.dev.js",
  "build": "webpack --config config/webpack.config.dev.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

执行 `npm run dev`, 可以在看见

- 控制台各种编译信息
- 编译完成后，自动打开浏览器，打开了 `http://localhost:8080`

当我们修改 `Content.jsx` 的文件并保存后，页面发生了重载

> 在代码编译的时候，我们可以发现 build 文件夹删除后就再也没有出现过了，那是因为使用编译后的代码并没有存在磁盘上，而是保存在了内存中


## HMR

[Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/)

简单来说，HMR 是按需更新

Webpack 在进行代码分割的时候，会将代码打包成一个个代码块。当我们对代码进行了修改后，Webpack 对代码重新打包后，并不会通知浏览器进行 full reload(全页面重新加载)，而只是对修改过的代码进行替换

HMR 优点

- Retain application state which is lost during a full reload.
    - 保全应用的状态
- Save valuable development time by only updating what's changed.
    - 节省开发的时间，因为只会更新变化的代码
- Modifications made to CSS/JS in the source code results in an instant browser update which is almost comparable to changing styles directly in the browser's dev tools.
    - 对样式的修改结果显示快

### 开启 HMR

- 使用 Webpack 本身自带的 HMR 插件
- 配置 `devServer` 选项

> 由于需要配置 `devServer`, 从这里也可以知道，配置 HMR 是需要在配置好 webpack-dev-server 的基础上进行

配置文件

```diff
...
const CleanWebpackPlugin = require('clean-webpack-plugin');
+ const webpack = require('webpack'); // 引入 webpack 模块

...

module.exports = {
  ...
  plugins: [
    ...
+   new webpack.HotModuleReplacementPlugin(), // 使用 webpack 自带的 HMR 插件
  ],
  devServer: {
    port: 8080,
    compress: true,
    historyApiFallback: true,
+   hot: true, // 开启 HMR 功能
    noInfo: false,
    open: true,
  },
};
```

## 测试

对 `app.jsx` 与 `Content.jsx` 的代码进行修改，通过**时间**来判断组件的状态是如何变化的

```js
// app.jsx

import React from 'react';
import Content from './Content.jsx';

export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      now: (new Date()).toString(),
    };
  }

  render() {
    return (
      <div>
        <div>An App</div>
        <Content now={this.state.now} />
      </div>
    );
  }
}
```

```js
// Content.jsx

import React from 'react';

export default class Content extends React.Component {
  render() {
    return (
      <div>
        I am content, too
        <br />
        {this.props.now}
      </div>
    );
  }
}
```

### 没有开启 HMR

随便修改 `app.jsx` 或者 `Content.jsx` 的显示文案后保存，可以看见页面上的时间都发生了改变

不对 `app.jsx` 与 `Content.jsx` 文件显示，直接保存，触发 Webpack 的重新编译，发现页面上的时间也是发生了变化，说明组件的状态发生了变化

### 开启 HMR

随便修改 `app.jsx` 或者 `Content.jsx` 的显示文案后保存，可以看见页面上的时间都发生了变化

不对 `app.jsx` 与 `Content.jsx` 文件显示，直接保存，触发 Webpack 的重新编译，发现页面上的时间并没有发生变化，即状态没有发生变化

对于第一种情况，随便修改 `app.jsx` 或者 `Content.jsx` 的显示文案后保存，我们修改了 `Content.jsx` 的内容而已，并没有修改 `app.jsx`, 而显示的时间状态是从 `app.jsx` 传入的，为什么也会引起状态的变化？

> 因为目前的代码比较少，Webpack 将所有的代码都打包到一个 JS 文件中，从编译的过程可以看到
> 所以 Content.jsx 中的代码发生了改变，引起了最终的代码块也改变了，导致了状态的丢失了




