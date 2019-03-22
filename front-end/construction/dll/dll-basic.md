# 动态链接库基本配置

> [Demo](https://github.com/pennyworthit/dll-demo)

## 什么是动态链接库

Dynamic Link Library

- 动态，指的是那些可以共用，可以独立封装成一个模块的
- 链接，指定就是引用

因此，动态链接库就是那些可以随时被引用的共用模块，它们独立打包成一个模块

对于这种模块，一般应该为项目的基础设施，即项目成功运行必不可少的部分。项目依赖这种模块，而这种模块确是与项目无关。而对于一个 React 项目来说，可以为这个项目创建的动态链接库，很大可能就是 React, React-Router, React-Redux

在前端使用动态链接库的原因，除了是可以将基础设施独立打包，另外一个重要的原因是提高项目的编译构建速度，避免每次编译时都将基础设施的代码编译一次。

## Webpack 中接入动态链接库

### 准备环境

#### 安装依赖

假设项目中需要使用到

- 框架相关
    - React 全家桶：React, React-Router, React-Redux
- 构建相关
    - Webpack
    - Webpack 命令行工具，我们将会直接使用命令向启动构建，也可以直接使用 Webpack 的编程接口进行构建，Create React App 就是这么做的
    - html-webpack-plugin Webpack 插件，自动生成 HTML 模版，动态嵌入脚本，样式依赖
    - @babel/core Babel 核心，用于转译 JS
    - babel-loader Webpack 中用于转译的 loader
    - @babel/preset-react React 代码转译规则
    - @babel/preset-env 高版本 JS 转译规则
    - webpack-dev-server 开发时的热更新

因此，我们需要安装如下依赖

```sh
npm i react react-dom react-router-dom redux react-redux
```

```sh
npm i -D webpack webpack-cli html-webpack-plugin @babel/core babel-loader @babel/preset-react @babel/preset-env webpack-dev-server
```

具体的配置这里省略了

### 配置 DLL

实际上，Webpack 内置了 DLL 的功能插件，只是一般情况下我们没有使用而已

在配置 DLL 前，我们首先要了解 Webpack 中关于 DLL 的两个插件

- DllPlugin
- DllReferencePlugin

前者是将相关代码打包成一个动态库，而后者是将前者打包出来的库链接到 Webpack 打包过程中的业务代码，因此，后者的名字中就含有了 `Reference`

#### 打包配置文件

在打包动态库的时候，Webpack 是需要一个配置文件，而这个配置文件并不是我们平时熟悉的构建配置文件，而是另一个新的文件

例如，我们新建一个打包动态库的配置文件 `webpack.dll.config.js`

```js
// 动态链接库构建配置文件

const path = require('path');
const webpack = require('webpack');
const DllPlugin = webpack.DllPlugin;

const publichPath = path.resolve(__dirname, '..', 'public');

module.exports = {
  // 入口文件
  entry: {
    reactFamily: [
      'react',
      'react-dom',
      'react-router-dom',
      'react-redux',
    ],
  },

  // 动态链接库输出
  output: {
    filename: 'vendor.js',
    path: path.join(publichPath, 'dll'),

    // 存放动态链接库的全局变量名称，如 react -> _dll_react
    library: '_dll_[name]',
  },
  plugins: [
    new DllPlugin({
      // 与上面 output.library 中保持一致
      // 此字段是 manifest.json 中的 name 字段值
      // 如 react.manifest.json 中，name 为 "_dll_react"
      name: '_dll_[name]',

      // 对于 react, 这个就是生成 react.manifest.json 的路径
      path: path.join(publichPath, 'dll/vendor.manifest.json'),
    }),
  ],
};


```

- 首先，需要引入 `DLLPlugin`, Webpack 将使用这个插件打包动态库
- `entry` 中，将告知打包的入口文件，`reactFamily` 对象（取名于 React 全家桶）中包含了各种与 React 相关的依赖，这些依赖将会打包成为一个动态库
- `output` 对象中，将告知输出位置，注意⚠️，这个 `output` 对象适用于打包出来的所有动态库，要知道的是，`entry` 对象是一个数组，因此可以一次打包多个动态库
    - `filename` 动态库的名称，指的是文件名，不包含路径，也可以使用 `[name].js` 来动态指定库名，其中 `[name]` 为 `entry` 对象的键名
    - `path` 动态库存放的路径（文件夹）
    - `library` 动态库的全局变量名称
- `plugins` 对象中，配置动态库打包规则
    - `name` 需要与 `output.library` 中的保持一致，它是 manifest.json 文件中的 `name` 字段值
    - `path` 生成每个动态库 manifest.json 的位置

> Webpack 打包项目代码时，遇到动态库，将会根据 manifest.json 中的 `name` 进行匹配

#### 配置链接

上面的步骤配置好了 Webpack 如何打包出动态库，接下来就需要配置动态库如何链接到项目代码中，这部分的配置就在我们熟悉的 Webpack 配置文件中进行

例如，我们的 Webpack 配置文件为 `webpack.config.js`

首先，我们需要引入链接动态库的文件

```js
const webpack = require('webpack');
const DllReferencePlugin = webpack.DllReferencePlugin;
```

然后在 `plugins` 中配置规则

```js
module.exports = {
  ...
  plugins: {
    ... // 其他插件
    
    // 动态链接库
    // 每个链接库对应一个 DllReferencePlugin 实例
    new DllReferencePlugin({
      manifest: path.join(paths.appDll, 'vendor.manifest.json'),
    }),
  },
};
```

- 配置如何链接动态库的规则，实际上只是告诉 Webpack 如何找到每个动态库的各自的 manifest.json 文件，因此可以将 manifest 看作是动态库与使用者的一个中介，动态库与使用方不需要直接交涉
- 要注意的是，每链接一个动态库，就需要新建一个 `DllReferencePlugin` 实例

> 动态链接库无论在开发环境还是生产环境上都可以使用，但是通过一番搜索发现，看到很多都是将动态链接库使用在开发环境上，而生产环境上，一般都是用 Webpack 自带的代码分割
> 
> 打包成动态链接库，就可能会将多个依赖打包成一个库，此时动态库是无法使用代码分割的，而请求就会将整个动态库发送到客户端，因为这个动态库有可能会很大，所以造成响应的体积很大。
> 
> 在 Webpack 4 中，代码分割使用了 `SplitChunk`, 能充分发挥按需加载的效能

#### 如何请求动态库

由于动态库是分开的，因此，我们要在 HTML 页面中加入对动态库请求的脚本，如

```html
<script src="./dll/vendor.js"></script>
```

也可以使用 `html-webpack-plugin` 动态配置

#### 打包动态库

为了方便打包动态库，一般还会创建打包命令

```js
"build:dll": "webpack --mode=production --config ./config/webpack.dll.config.js"
```

> 通过对比发现，当使用动态库时，打包出来的业务代码只有几 KB 的大小，而不使用动态库，则打包出来的业务代码有 100 多KB

---

当我们使用 DLL 时，执行 `nom run dev`

- 尝试在 `index.html` 中注释掉 `<script src="./dll/vendor.js"></script>`, 可以发现控制台报错

    ```
    Uncaught ReferenceError: _dll_reactFamily is not defined
    ```

- 而取消注释后，返现页面正常



