# Node


## npm

- [将 Node 包发布到 npm](./npm-publish.md)

## 前端构建

- [构建简单的脚手架 React](./create-boilerplate.md)
- [构建命令行工具](./build-command-line-tool.md)
- [徒手配置 React 与 Webpack 组合](./webpack-with-react.md)
- [自动清理打包文件](./webpack-auto-clean-build.md)
- [代码分割](./webpack-code-splitting.md)
- [开发时热更新](./webpack-hot-dev.md)
- [样式的支持：添加样式，样式文件分离，样式文件压缩](./webpack-style-support.md)
- [样式的进一步处理：CSS 模块化，支持 SCSS, 支持 PostCSS](./webpack-style-support-advance.md)

善后工作

- [分拆 Webpack 配置](./webpack-config-merge.md)

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

## 实践

- [JWT 验证 Demo](./jwt-authentication-demo.md)
- [Node App 部署](./node-app-deploy.md)
- [使用 Node 搭建简单的 WebSocket 服务](./node-websocket.md)

## 链接

- Webpack 打包 Node 后台应用
    - [Backend Apps with Webpack (Part I)](https://jlongster.com/Backend-Apps-with-Webpack--Part-I)

