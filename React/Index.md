# React

- [React (Native) 项目中植入 eslint](./react-or-native-with-eslint.md)

## React

- [React Start Memo](./react/react-start-memo.md)
- [将 React 项目部署到 GitHub Pages](./react/react-github-pages.md)
- [遵守 React 内置的 ESLint 规则](./react/react-eslint-rules.md)
- [React 简单的服务器渲染例子](./react/react-ssr.md)
- [React 组件生命周期](./react/react-life-cycle.md)
- [React 代码分割](./react/react-code-splitting.md)
- [React 使用 react loadable 进行代码分割实践](./react/react-code-splitting-in-action.md)

### Webpack

- [Webpack + React 搭建工程](./react/webpack-react-project.md)
- [Webpack 基本了解](./react/webpack-basic.md)
- [Webpack 构建速度优化](./react/webpack-build-optimize.md)

### React-Redux

- [React-redux 基本要点](./react/react-redux-basic.md)
- [React-Redux 异步使用](./react/react-redux-async.md)
- [在 Chrome 中调试 Redux](./react/react-redux-chrome.md)
- [React Redux 的组件递归调用](./react/react-redux-recursive.md)

### React-Router

- [React Router 简单使用](./react/react-router-base.md)

## React Native

- [代码工具库](./react-native/codebase.md)
- [iOS 原生集成 React Native](./react-native/iOS-integrate-react-native.md)
- [React Native flex 布局](./react-native/react-native-flex-layout.md)
- [React Native iOS 打包](./react-native/react-native-packup-ios.md)
- [React Native 调用 iOS 原生模块](./react-native/react-native-invoke-ios-api.md)
- [React Native for Android](./react-native/react-native-for-android.md)
- [React Native 中使用 TypeScript](./react-native/react-native-typescript.md)

## 前端构建

- [构建简单的脚手架 React](./react/create-boilerplate.md)
- [构建命令行工具](./react/build-command-line-tool.md)
- [简单的自动化构建与发布](./react/simple-auto-build-publish.md)

### 徒手配置 React 与 Webpack 组合

- [基础配置](./react/webpack-with-react.md)
- [自动清理打包文件](./react/webpack-auto-clean-build.md)
- [代码分割](./react/webpack-code-splitting.md)
- [开发时热更新](./react/webpack-hot-dev.md)
- [样式的支持：添加样式，样式文件分离，样式文件压缩](./react/webpack-style-support.md)
- [样式的进一步处理：CSS 模块化，支持 SCSS, 支持 PostCSS](./react/webpack-style-support-advance.md)

善后工作

- [分拆 Webpack 配置](./react/webpack-config-merge.md)
- [配置 eslint 语法检测](./react/integrate-with-eslint.md)
- [配置源代码查看，调试](./react/integrate-source-map.md)

[Demo](https://github.com/pennyworthit/react-webpack-from-0) 及其分支重点，分先后顺序

1. master & basic 构建项目，编写最基本的 Webpack 配置
2. integrate-react 引入 React, 并在浏览器中运行最简单的 React, 引入了 `html-webpack-plugin` 插件
3. auto-clean-build 使用 `clean-webpack-plugin` 实现构建前自动清理
4. 代码分割
    - code-splitting 进行了简单的代码分割配置
    - code-splitting-default 使用 Webpack 默认的代码分割配置，之后的分支都基于这个分支上进行
5. hot-dev 开发时的热更新与 HMR 开启
6. style-support 支持 CSS, CSS 文件导出，CSS 代码压缩 
7. style-support-2 支持 CSS 模块化，支持 SCSS, 支持 PostCSS
8. webpack-config-merge 分拆 Webpack 配置
9. integrate-eslint 配置 eslint 语法检测
10. source-map 配置开发环境下的源代码查看配置

## Common

- [🔧 非一级页面刷新返回 404](./react/refresh-get-404.md)

## 链接

- 如何在 CRA 中使用 CSS module
    - [How to Use CSS Modules with Create React App](https://medium.com/nulogy/how-to-use-css-modules-with-create-react-app-9e44bec2b5c2)
- 如何在 TypeScript 中使用 CSS module
    - [How to use CSS Modules with TypeScript and webpack](https://medium.com/@sapegin/css-modules-with-typescript-and-webpack-6b221ebe5f10)

