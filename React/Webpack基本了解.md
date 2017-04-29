# Webpack 基础配置

[From](https://www.qianduandao.com)

- [核心概念](#核心概念)
	- [入口Entry](#入口Entry)
	- [出口Output](#出口Output)
	- [加载器Loaders](#加载器Loaders)
	- [插件Plugins](#插件Plugins)
- [配置webpack-dev-server并启用热模块替换](#配置webpack-dev-server并启用热模块替换)
	- [本地安装webpack-dev-server](#本地安装webpack-dev-server)
	- [使用NodeAPI来启动webpack-dev-server](#使用NodeAPI来启动webpack-dev-server)
- [添加Loaders](#添加Loaders)
	- [添加babel-loader，用于将ES6,ES7转换为ES5模块](#添加babel-loader，用于将ES6,ES7转换为ES5模块)
	- [添加sass-loader,将SASS文件转译成CSS的Loader](#添加sass-loader,将SASS文件转译成CSS的Loader)
- [React脚手架](#React脚手架)

## 核心概念

- 入口 (Entry)
- 出口 (Output)
- 加载器 (Loaders)
- 插件 (Plugins)

### 入口Entry

使用 Webpack 创建应用的所有 _依赖关系_ ， _依赖关系_ 的起始点就是入口 Entry，告知 Webpack 按照入口的依赖关系绑定资源。

入口是应用启动的第一个文件

```js
module.exports = {
	entry: './file.js',
};
```

### 出口Output

出口用于告知 Webpack，在应用打包完毕后，如何处理打包文件及存放的位置，分别使用 output.path 和 output.filename

```js
const path = require('path');
module.exports = {
	entry: './file.js',
	output: {
		path: path.resolve(__dirname, 'dist'), // __dirname 文件的当前路径，并不是执行是的路径
		filename: 'bundle.js',
	},
}
```

### 加载器Loaders

Webpack 将所有资源视为一个模块(如 .css, .html, .scss, .jpg)

当有文件添加到依赖关系中，Webpack 中的 Loaders 会将这些新增文件转换成一个模块

Loaders 的目的

- 识别并确认文件应该被什么 Loader 进行转译
- 转译文件

```js
const path = require('path');
module.exports = {
	entry: './file.js',
	output: {
		path: path.resolve(__dirname, 'dist'), // __dirname 文件的当前路径，并不是执行是的路径
		filename: 'bundle.js',
	},
	module: {
		rules: [
			{
				test: /\.(js|jsx)$/,
				use: 'babel-loader',
			}
		]
	},
}
```

### 插件Plugins

Loaders 是以单个文件作为基础进行操作，Plugins 对整体进行操作

插件可以被多次使用，并作用于不同的目的，因此，创建实例并放进 Plugins 数组中比较方便

```js
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 或 yarn 安装的插件
const webpack = require('webpack');
const path = require('path');
module.exports = {
	entry: './file.js',
	output: {
		path: path.resolve(__dirname, 'dist'), // __dirname 文件的当前路径，并不是执行是的路径
		filename: 'bundle.js',
	},
	module: {
		rules: [
			{
				test: /\.(js|jsx)$/,
				use: 'babel-loader',
			}
		]
	},
	plugins: [
		new webpack.optimize.UglifyJsPlugin(), // 压缩 JS
		new HtmlWebpackPlugin({template: './src/index.html'}), // 生成 HTML 模版
	],
}
```

## 配置webpack-dev-server并启用热模块替换

热模块替换((Hot Module Replacement enabled))，说白了，就是实时看到修改内容的变化结果，不需要刷新页面的那种

### 本地安装webpack-dev-server

```shell
npm install -D  webpack-dev-server
```

启动 webpack-dev-server 的2种方式

- 命令行
- Node API

### 使用NodeAPI来启动webpack-dev-server

#### 新建webpack.config.js文件

按照上面的核心部分配置

```js
const path = require('path');

module.exports = {
  entry: "./src/entry.js",
  output: {
  path: path.resolve(__dirname, 'build'),
    filename: "bundle.js"
  },
}
```

#### 根目录新建dev-server.js

根据 [From](https://www.qianduandao.com) 设置了 dev-server.js 的内容 

```js
var WebpackDevServer = require('webpack-dev-server');
var webpack = require('webpack');
var config = require('./webpack.config.js'); // 引入我们上面简单的配置文件
var path = require('path');
var compiler = webpack(config);
var server = new WebpackDevServer(compiler,{
    //在这儿配置webpack-dev-server
    hot:true,
    filename: config.output.filename,
    publicPath: config.output.publicPath,
    stats: {
        colors: true
    }
})
server.listen(8080,'localhost',()=>{});
```

上文对 [官方的 dev-server](https://github.com/webpack/webpack-dev-server/blob/master/examples/node-api-simple/server.js) 事例作了修改

```js
"use strict";
const Webpack = require("webpack");
const WebpackDevServer = require("../../lib/Server");
const webpackConfig = require("./webpack.config");
const compiler = Webpack(webpackConfig);
const server = new WebpackDevServer(compiler, {
	stats: {
		colors: true
	}
});
server.listen(8080, "127.0.0.1", function() {
console.log("Starting server on http://localhost:8080");
});
```

#### 改进webpack.config.js

添加 webpack 实例

```js
const webpack = require('webpack');
```

添加热替换插件

```js
plugins: [
	new webpack.HotModuleReplacementPlugin(),
],
```

修改 entry 为

```js
entry: [
	'./src/entry.js',
	'webpack-dev-server/client?http://127.0.0.1:8080',
	'webpack/hot/dev-server',
],
```

修改 output 为 

```js
output: {
	path: path.resolve(__dirname, 'build'),
	filename: "bundle.js",
	publicPath: '/build/',
},
```

修改完的文件内容为 

```js
const path = require('path');
const webpack = require('webpack');

module.exports = {
  entry: [
      './src/entry.js',
      'webpack-dev-server/client?http://127.0.0.1:8080',
      'webpack/hot/dev-server',
  ],
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: "bundle.js",
    publicPath: '/build/',
  },
  plugins: [
      new webpack.HotModuleReplacementPlugin(),
  ],
}
```

#### 在/src/entry.js中追加内容

```js
if (module.hot) {
    module.hot.accept();
}
```

修改完的文件内容为

```js
import stranger from './stranger';
let saying = stranger.words + ',' + "I'm " + stranger.name;
document.querySelector('#entry').innerHTML = saying;

if (module.hot) {
    module.hot.accept();
}
```

#### 修改package.json中的scripts

添加 script

```js
"dev": "node dev-server.js",
```

修改完的文件 scripts 部分内容为

```js
"scripts": {
"build": "webpack",
"dev": "node dev-server.js",
"test": "echo \"Error: no test specified\" && exit 1"
},
```

#### 测试

在 package.json 所在的目录，执行命令

```shell
npm run dev
```

修改 stranger.js 等文件，即可看到页面上发生相应的变化


## 添加Loaders

在 webpack.config.js 中添加 module

```js
module: {
	rules: [],
}
```

### 添加babel-loader，用于将ES6,ES7转换为ES5模块

1. 安装

	```shell
	npm install -D babel-loader babel-core
	```
	
	在 webpack.config.js 中添加 rules
	
	```js
	{
		test: /\.js$/,
		exclude: /node_modules/,
		use: "babel-loader",
	}
	```
	
	test 使用 **正则表达式** 来匹配要被 Loader 编译的资源
	use 指定 test 的资源编译时使用的 loader
	exclude: 指定不匹配也不编译的资源

2. 根目录创建 .babelrc 文件

	需要先安装 babel-preset-env
	
	babel-preset-env 可根据配置的目标运行环境，自动开启需要的 babel 插件

	```shell
	npm install babel-preset-env --save-dev
	```
	
	修改 .babelrc 文件

	```json
	{
	    "preset": [
	        [
	            "env",
	            {
	                "targets": { "ie": 9 }
	            }
	        ]
	    ]
	}
	```

### 添加sass-loader,将SASS文件转译成CSS的Loader

安装 sass-loader，由于 sass-loader 依赖 node-sass，需要一同安装

```shell
npm install sass-loader style-loader css-loader node-sass -D
```

向 webpack.config.js 中的 rules 追加

```js
{
	test: /\.scss$/,
	use: [
		{loader: "style-laoder"}, // 在 HTML 中创建 style 节点，内嵌 CSS
		{loader: "css-loader"}, // 使 CSS 也具有像 JS 一样的模块化功能
		{loader: "sass-loader"}, // 将 SASS 编译成 CSS
	],
}
```

最后，webpack.config.js 的内容为 

```js
const path = require('path');
const webpack = require('webpack');

module.exports = {
  entry: [
      './src/entry.js',
      'webpack-dev-server/client?http://localhost:8080',
      'webpack/hot/dev-server',
  ],
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: "bundle.js",
    publicPath: '/build/',
  },
  plugins: [
      new webpack.HotModuleReplacementPlugin(),
  ],
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: "babel-loader",
      },
      {
        test: /\.scss$/,
        use: [
            {loader: "style-laoder"}, // 在 HTML 中创建 style 节点，内嵌 CSS
            {loader: "css-loader"}, // 使 CSS 也具有像 JS 一样的模块化功能
            {loader: "sass-loader"}, // 将 SASS 编译成 CSS
        ],
      },
    ],
  },
}
```

## React脚手架

虽然了解了 Webpack 的基本知识，但是从头开始写一个 package.json 和 webpack.config.js 并不是一件愉快的事，特别是对于初学 React 的人来说，因此，可以先使用一些已经写好的模版，先将 React 部分的知识学习好，然后再深入了解 Webpack。

[脚手架](https://github.com/ruanyf/react-babel-webpack-boilerplate)

