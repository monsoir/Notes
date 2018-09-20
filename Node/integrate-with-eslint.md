# 配置 eslint 语法检测

## 创建 eslint 规则

配置 eslint 的规则，可以直接参考 [macOS 下 React (Native) 项目中植入 airbnb 代码规范](../React/react-or-native-with-eslint.md)

## 修改代码

由于直接继承了 Airbnb 的 JavaScript 规范，当中要求其中以下几点

1. `import` 语句中不允许包含 `.js`, `.jsx` 后缀
- 从 `props`, `state` 中获取属性需要使用对象析构
- 需要使用 `prop-types` 进行类型检查，其中需要安装 `prop-types` 模块
- 可以使用纯函数的组件，就使用纯函数
- 不允许 undefined 的属性，如 `document`
- 不允许在 `.js` 文件中使用 JSX 语法，在这里，集中体现在 `index.js` 中使用了 `<App />`

进行直接的代码修改，可以改善 1, 2, 3, 4 点，修复 eslint 的错误警告，但对于 5, 6 点，我们需要修改 eslint 的配置文件来进行修复

## 修改 eslint 规则

在 `./eslintrc.js` 文件中实施以下修改

```diff
module.exports = {
  "extends": "airbnb",
+ "globals": { // 全局变量 document 的报错, false 表示属性不可以被重写
+   "document": false,
+ },
+ "rules": { // 解决 index.js 中不能使用 JSX
+   "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
+ }
};
```

以上两个规则的修改分别根据

- [Specifying Globals](https://eslint.org/docs/user-guide/configuring#specifying-globals)
- [Restrict file extensions that may contain JSX (react/jsx-filename-extension)](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-filename-extension.md)

## 修改 Webpack 配置文件

由于在修改代码的过程中，我们去除了 `.js`, `.jsx` 文件后缀，当我们直接使用 Webpack 进行打包时，将会产生错误，提示不能查找到文件，如

```
ERROR in ./src/index.js
Module not found: Error: Can't resolve './app' in '/Volumes/Sparrow/my-demo/src'
```

此时，我们需要对 Webpack 进行配置 `webpack.config.base.js`

```diff
...
module.exports = {

  ...
  
+ resolve: {
+   extensions: ['.js', '.jsx'],
+ },
  module: {
    rules: [
      ...
    ],
  },
  
  ...
  
};
```

Webpack 配置文件中的 `resolve` 属性中也包含了多种属性，`resolve` 大致负责用来重新查找或解析模块，如重新定义模块的查找路径 (`resolve.alias`), 重新定义文件后缀名 `resolve.extensions`

在上面的配置中，即表示，当文件缺少后缀名时，使用 `.js` 和 `.jsx` 尝试解析

## 修改 package.json 脚本命令

当使用 VSCode 进行编辑时，eslint 可以在开发者一边编码一边检查，但同时我们也希望可以使用命令来进行整个项目代码检查，此时，就可以向 `package.json` 中添加一个命令来实现

```diff
"scripts": {
+   "lint": "eslint ./src",
    "dev": "webpack-dev-server --inline --progress --config config/webpack.config.dev.js",
    "build": "webpack --config config/webpack.config.prod.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

添加的命令表示使用 eslint 对 `./src` 目录下的代码进行检查


