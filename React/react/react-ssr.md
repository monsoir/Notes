# React App 简单的服务器渲染

[例子](https://github.com/pennyworthit/react-ssr.git)

## 原理概括

1. 实质上是通过 Node.js 上的 Express 或类似的服务端框架，搭建一个输出 HTML 字符串的服务器
2. 通过 React 本身提供的 `renderToString()` 方法（还有其他方法）将 React 控件转换为 HTML 字符串

## 搭建项目

创建默认的 package.json 文件

```sh
npm init -y
```

写入依赖和脚本命令

```json
"scripts": {
    "start": "node ./dist/server",
    "build": "webpack",
    "test": "echo \"Error: no test specified\" && exit 1"
},
"devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.4",
    "babel-preset-env": "^1.6.1",
    "babel-preset-react": "^6.24.1",
    "webpack": "^3.8.1",
    "webpack-node-externals": "^1.2.0"
},
"dependencies": {
    "express": "^4.16.3",
    "react": "^16.3.1",
    "react-dom": "^16.3.1"
}
```

> 当然，这里可以使用 `npm install xxx` 命令来安装，但是由于此时使用的 npm v5.8.0 在安装 webpack 时出现 `write after end` 错误，但通过手动写 package.json 的方法安装可以安装成功

搭建如下目录 `tree -I 'node_modules|dist'`

```
.
├── package-lock.json
├── package.json
├── src
│   ├── client
│   │   ├── App.js
│   │   ├── Html.js
│   │   └── index.js
│   └── server.js
└── webpack.config.js
```

## 编写源代码

### 📃 `App.js`

```jsx
import React from "react";

const styles = {
    textAlign: 'center',
    backgroundColor: 'red',
};

const App = () => (
    <div style={{backgroundColor: 'red'}}>🤨😒</div>
);

export default App;
```

`App.js` 目前只是简单地显示两个 emoji, 同时加了点红色底色

### 📃 `index.js`

```jsx
import React from 'react';
import { render } from 'react-dom';
import App from './App';

render(<App />, document.getElementById('app'));
```

`index.js` 中，使用原生的 JavaScript 方法，获取到页面 HTML 中的 `app` 修饰的标签，并使用 `<App />` 这个 React 组件来作为 `app` 元素的内容

### 📃 `Html.js`

```js
const Html = ({ body, title }) => (`
    <!DOCTYPE html>
    <html>
    <head>
        <title>${title}</title>
    </head>
    <body style="margin:0">
        <div id="app">${body}</div>
    </body>
    </html>
`);

export default Html;
```

`Html.js` 中，目标作用是提供一个函数，这个函数中包含了一个 HTML 页面的基础源代码，同时，这个函数接受两个参数，`body` 和 `title`, 分别是 React 转换后的 HTML 字符串与页面标题

而函数最后的输入，就是页面的 HTML 源码字符串，到时直接服务端直接返回这些字符串，不需要再有客户端进行渲染

### 📃 `server.js`

```js
import express from 'express';
import React from 'react';
import { renderToString } from 'react-dom/server';
import App from './client/App';
import Html from './client/Html';

const port = 3000;
const server = express();

server.get('/', (req, res) => {
    const body = renderToString(<App />);
    const title = 'Server side rendering';

    res.send(Html({
        body,
        title,
    }));
});

server.listen(port, () => {
    console.log('listening...')
});
```

`server.js` 中，主要就是搭建一个服务器，用于接受用户的请求，并对 React 页面进行渲染后，得出字符串并返回

`const body = renderToString(<App />);` 首先，这里将 React 页面，即 `<App />` 这个组件转换为了 HTML 字符串，并赋值到 `body`

`res.send(Html({ body, title }));` 然后，通过之前定义的方法 `Html`, 将刚才 React 组件的 HTML 字符串拼接到 HTML 基础模版，形成一个完整的 HTML 字符串，并返回到客户端

## 配置 Webpack

```js
const webpack = require('webpack');
const nodeExternals = require('webpack-node-externals');
const path = require('path');

module.exports = {
    entry: './src/server.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'server.js',
        publicPath: '/',
    },
    target: 'node',
    externals: nodeExternals(),
    plugins: [
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: `'production'`
            }
        }),
    ],
    module: {
        loaders: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
            },
        ]
    }
};
```

- `entry` Webpack 打包入口
- `output` Webpack 打包后文件的输出目录
- `target` 由于 JavaScript 可以运行在浏览器端和服务端，因此，需要将设定好编译环境，由于是服务端渲染，也就是运行在服务端了
- `externals` 告诉 Webpack 不要打包的内容
- `loaders` 用于文件的转换，例如我们的 React 控件，使用的是 JSX 语法编写，但是 JavaScript 解释器并不理解 JSX, 于是需要 loaders 中提供的工具，将 JSX 语法转换为 JS 语法
- `plugins` 与 loaders 的作用相似，但可以比 loaders 做的更多，可以对文件进行加工，例如代码混淆，热加载等

## 配置 `/babelrc`

由于 Webpack 中使用了 Babel 来进行代码转换，而 Babel 会在项目中寻找 `.babelrc` 文件，因此，我们需要配置一下

```json
{
  "presets": ["env", "react"]
}
```

我们只需要转换服务端(env)和React(react)部分的代码

## 运行

配置完成后，我们可以通过

- `npm run build` 来进行代码的编译打包
- `npm start` 使用编译打包后的代码，开启服务端

最后通过访问 `localhost:3000` 可以加载出服务端渲染的页面

## References

- [The simple guide to server-side rendering React with styled-components](The simple guide to server-side rendering React with styled-components)
- [ReactDOMServer](https://reactjs.org/docs/react-dom-server.html)

