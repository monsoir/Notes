# Webpack + React 搭建项目

大多数教程都直接抛出 webpack.config.js 的配置文件，让同学不明所以地复制粘贴。后来，找到[这篇文章](https://www.codementor.io/tamizhvendan/beginner-guide-setup-reactjs-environment-npm-babel-6-webpack-du107r9zr)，大致地讲述了 webpack.config.js 中配置项的由来，还是不错的。

[from](https://www.codementor.io/tamizhvendan/beginner-guide-setup-reactjs-environment-npm-babel-6-webpack-du107r9zr)

[TOC]

## 创建项目文件

运行命令

```shell
mkdir react-hello-world
cd react-hello-world
npm init # 这个命令会在终端中不断引导用户创建

npm init -y # 这个命令直接创建一个 package.json，全部使用默认值
```

## 安装，配置 Webpack

### 安装 Webpack

运行命令

```shell
# 将 Webpack 安装到本地（非全局），并将配置写入到 package.json
npm install webpack --save-dev
```

### 配置 Webpack

#### 创建 webpack.config.js

```shell
touch webpack.config.js
```

#### 写入配置

```js
var webpack = require('webpack');
var path = require('path');

var BUILD_DIR = path.require(__dirname, 'src/client/public');
var APP_DIR = path.resolve(__dirname, 'src/client/app');

var config = {
    entry: APP_DIR + '/index.jsx',
    output: {
        path: BUILD_DIR,
        filename: 'bundle.js'
    }
};

module.exports = config;
```

##### MARK

- webpack.config.js 最小的需求，就只需要指明 entry 和 output 属性
- entry 属性，相当于 Webpack 的 Main 方法。而且，Webpack 支持多个 entry
- output 属性，指明当 Webpack 的 build 操作完成之后的任务
- APP_DIR 指明了项目的源代码目录
- BUILD_DIR 指明了 bundle file 输出的目录，bundle file 是之后生成的压缩文件

#### 测试一下 Webpack

##### 在 ./src/client/app 中创建 index.jsx

写入以下内容

```js
console.log('hello world!');
```

##### 运行命令

先建立 src/client/public 文件夹

```shell
./node_modules/.bin/webpack -d
```

在开发模式下运行 webpack，生成 bundle.js 和它的关联文件 bundle.js.map，并输出在 src/client/public (这是在 webpack.config.js 中配置的)

##### 结果

可以看到 src/client/public 中生成了 bundle.js 文件

#### 创建一个 html 来展示一下结果

在 src/client 中创建一个 index.html，并在其中引入上面生成的 bundle.js

```html
<html>
  <head>
    <meta charset="utf-8">
    <title>React.js using NPM, Babel6 and Webpack</title>
  </head>
  <body>
    <div id="app" />
    <script src="public/bundle.js" type="text/javascript"></script>
  </body>
</html>
```

最后，应该可以在控制台中看到 Hello World 的输出

## 配置 Babel-Loader

### 安装依赖包

```shell
npm i babel-loader babel-preset-es2015 babel-preset-react --save-dev
```

#### MARK

babel-preset-es2015 和 babel-preset-react 是 babel-loader 的插件，用于转译 ES6 和 JSX 语法

### 配置 Babel

babel-loader 需要配置，来获知使用 ES6 和 JSX 插件

创建 .babelrc 文件

```shell
touch .babelrc
```

写入配置

```json
{
  "presets" : ["es2015", "react"]
}
```

#### 再次配置 webpack.config.js

```js
// Existing Code ....
var config = {
  // Existing Code ....
  module : {
    loaders : [
      {
        test : /\.jsx?/,
        include : APP_DIR,
        loader : 'babel-loader'
      }
    ]
  }
}
```

##### MARK

loaders 属性接受一个包含一个或多个 loader 的数组，每个 loader 对象应该提供的属性：

- test 使用正则表达式，提供需要转译的文件的扩展名
- include 指定从哪些文件夹中转译符合 test 中扩展名的文件（ 可以使用 exclude 来排除）
- loader 表示 loader 的名称


#### 再写一点 React 来测试一下 Babel

安装 react-dom

```shell
npm install react-dom --save-dev
```

更改 index.jsx，修改成这样

```js
'use strict';

import React from 'react';
import {render} from 'react-dom';

class App extends React.Component {
  render () {
    return <p> Hello React!</p>;
  }
}

render(<App/>, document.getElementById('app'));
```

运行命令

```shell
./node_modules/.bin/webpack -d
```

发现出错了：

Module build failed: Error: Cannot find module 'babel-core'

需要安装 babel-core

```shell
npm install babel-core -D
```

其实，这里还有其他错误导致无法成功转译，因为依赖安装错地方了，重新运行一下命令

```shell
npm install react react-dom -S
```

现在，重新运行 webpack，应该能成功了

```shell
./node_modules/.bin/webpack -d
```

## 让 Webpack 更加自动化

### 让 Webpack 自动监听文件变化，并自动将文件转译

运行命令

```shell
./node_modules/.bin/webpack -d --watch
```

现在，Webpack 处于监听模式，当检测到文件变化时，将自动打包。更改完文件之后，可直接在浏览器中刷新并看到变化

若不想刷新浏览器就看到更改结果，可以使用 react-hot-loader

### 通过 npm 使得 Webpack 更加自动化

优化 ./node_modules/.bin/webpack 命令

更改 package.json 文件，修改 script 属性

```json
"scripts": {
    "dev": "webpack -d --watch",
    "build": "webpack -p"
},
```

关于 Webpack 的命令选项，查看 [官方文档](https://webpack.github.io/docs/cli.html#development-shortcut-d)


# npm install 选项参考

-S --save dependencies
-D --save-dev devDependencies
-O --save-optional optionalDependencies

