# 徒手配置 React 与 Webpack 组合

## 安装基本依赖

```sh
# react 部分
npm i react react-dom

# webpack 部分
npm i webpack webpack-cli
```

## 创建项目结构

创建相应文件夹，得到最简单的项目结构

![](https://ws4.sinaimg.cn/large/006tNbRwly1fuyy8msac6j30gs0cwwfm.jpg)

结构解析

- build 使用 webpack 打包后的 js 文件
- config 存放项目构建的配置文件，目前只针对开发环境的配置，故只有 `webpack.config.dev.js`
- public 静态资源文件引用时的路径
- src 存放源代码文件

## 编写打包配置文件

对于最简单的项目，配置文件是重点，目前只针对开发环境的配置，只有 `webpack.config.dev.js`

```js
// webpack.config.dev.js

const path = require('path');

// 获取到项目的根目录路径
const rootPath = path.join(__dirname, '..');

// 获取到静态资源文件夹的路径
const publicPath = path.join(rootPath, 'public');

module.exports = {
  // 入口文件路径
  entry: path.join(rootPath, 'src/app.js'),
  
  // 打包输入文件配置
  output: {
    filename: '[name].[hash].js',
    path: path.join(rootPath, 'build'),
    publicPath,
  },
};
```

`__dirname` 返回当前文件的所在的文件夹路径

`path.join(argv0, argv1, ...)` 从 argv0 开始，不断向上回溯路径

`[name].[hash].js`

- [name] 文件的原名
- [hash] 打包完成后会自动加上的 hash 值，每次打包都会发生改变

## 配置打包脚本

```diff
// package.json
...
"scripts": {
+ "build": "webpack --config config/webpack.config.dev.js --mode development",
  "test": "echo \"Error: no test specified\" && exit 1"
},
...
```

执行 `npm run build`, Webpack 会从 `app.js` 文件开始出发，将所有设计到的文件进行打包，最后输出到 `build` 文件夹中

> `--mode development` 为开发模式下，会包含 sourceMap 信息，用于调试
> `--mode production` 为生产环境模式，会尽量简化打包后的代码

实际上，可以在 Webpack 配置文件中直接配置环境，即

```diff
// webpack.config.dev.js

const path = require('path');
const rootPath = path.join(__dirname, '..');

const publicPath = path.join(rootPath, 'public');

module.exports = {
  entry: path.join(rootPath, 'src/app.js'),
  output: {
    filename: '[name].[hash].js',
    path: path.join(rootPath, 'build'),
    publicPath,
  },
+ mode: 'development'
};
```

这样，`package.json` 中的命令可以简化成

```diff
// package.json
...
"scripts": {
- "build": "webpack --config config/webpack.config.dev.js --mode development",
+ "build": "webpack --config config/webpack.config.dev.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
...
```

## 添加对 React 的支持

使用 React 编写前端页面时，我们都是使用 JSX 语法，但浏览器本身并不支持 JSX 语法，需要我们对 JSX 代码转换为 JS 代码

在 Webpack 中，我们可以使用 [loader](https://webpack.docschina.org/concepts/#loader) 来进行这一步的操作

实际上，Webpack 只是一个打包工具，本身并包含代码转译的功能，在此处的语境，即 Webpack 本身并不支持将 JSX 代码转译为 JS 代码。那将 JSX 代码转译为 JS 代码，我们需要借用另一个工具 [Babel](https://babeljs.io) 来进行实现

### 安装配置 Babel

关于如何在 Webpack 中使用 babel-loader, Webpack 官网给出了 [示范](https://webpack.js.org/loaders/babel-loader/#src/components/Sidebar/Sidebar.jsx)

```sh
# 安装 Babel 在 Webpack 中的 loader
# 安装 Babel 核心库
# 安装 Babel 的转译规则集
npm i -D babel-loader @babel/core @babel/preset-env @babel/preset-react
```

> babel-core Babel 的核心
> babel-cli 通过命令行使用 Babel 的话，需要安装，在这里面，我们只通过 Webpack 调用，因此不安装了

#### 在 Webpack 中配置 Babel

在 Webpack 中使用 Babel, 我们需要使用的是 loader

```diff
const path = require('path');

const rootPath = path.join(__dirname, '..');
const publicPath = path.join(rootPath, 'public');

module.exports = {
- entry: path.join(rootPath, 'src/app.js'),
+ entry: path.join(rootPath, 'src/index.js'),
  output: {
    filename: '[name].[hash].js',
    path: path.join(rootPath, 'build'),
    publicPath,
  },
  mode: 'development',
+ module: {
+   rules: [
+     {
+       test: /\.(js|jsx)$/,
+       use: {
+         loader: 'babel-loader',
+         options: {
+           presets: ['@babel/env', '@babel/react'],
+         },
+       },
+       exclude: [
+         path.join(rootPath, 'node_modules'),
+       ],
+     },
+   ],
+ },
};
```

上述 loader 配置项的意思是：

对于 `.js` 或 `.jsx` 结尾的文件，使用 babel-loader 进行转译，转译规则集为 `env`, `react`, 但不包括 `node_modules` 中的文件

loader 的配置在于 module 属性内，即

```js
module.exports = {
  ...
  module: {
    rules: [
      // rules 中添加 loader
      {
        test: /a regex/, // 一个正则表达式，用于确定哪些文件应用此 loader
        use: {
          loader: 'an loader', // loader 的名称
          options: { // loader 的其他选项
            ...
          },
          // 其他配置项
        },
        exclude: [
          // 忽略的文件，如 node_modules 中的库，就不用再转译了
        ],
      },
      {
        // 另一个 loader 配置
      },
    ],
  }
  ...
};
```

#### Babel 配置规则

在 Babel 的配置中，有一个配置项 `presets` 非常重要

当你决定使用 Babel 对你写的代码进行转译时，`presets` 配置就决定你可以使用哪种版本的 JS 进行编码，如 `babel-presets-es2015`, `babel-presets-react`, 就分别指明了，在你的代码中可以使用 ES6 语法，React 的 JSX 语法进行编码

这些规则集都是需要根据我们自身的需求进行安装，安装了 Babel 并不会顺带安装这些规则集

实际上，在 Babel 7 中，有一个新的规则集 `babel-env`, 这个规则集涵盖了

- `babel-preset-es2015`
- `babel-preset-es2016`
- `babel-preset-es2017`

具体可以见 [babel-preset-es2015 -> babel-preset-env](http://babeljs.io/docs/en/env/). 因此，我们可以直接使用 `preset-env` 就可以了

### 写点 React 代码

> 由于之前的 `app.jsx` 与 `app.js` 文件名在 `import` 的时候可能会引起误会，所以将 `app.js` 重命名为 `index.js`

```js
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './app.jsx';

ReactDOM.render(<App />, document.body);
```

```jsx
import React from 'react';

export default class App extends React.Component {
  render() {
    return (
      <div>An App</div>
    );
  }
}
```

同时，我们也会对原来 `package.json` 进行一点修改，使得在 Webpack 打包前，将之前的文件都删除，以便我们观察文件的变化

```diff
"scripts": {
- "build": "webpack --config config/webpack.config.dev.js",
+ "build": "rm build/* && webpack --config config/webpack.config.dev.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

执行 `npm run build`, 可以观察到 build 文件夹中的内容发生了变化

