# 工程构建

- [构建简单的脚手架 React](./webpack-react/create-boilerplate.md)
- [构建命令行工具](./webpack-react/build-command-line-tool.md)
- [简单的自动化构建与发布](./webpack-react/simple-auto-build-publish.md)

## 徒手配置 React 与 Webpack 组合

- [基础配置](./webpack-react/webpack-with-react.md)
- [自动清理打包文件](./webpack-react/webpack-auto-clean-build.md)
- [代码分割](./webpack-react/webpack-code-splitting.md)
- [开发时热更新](./webpack-react/webpack-hot-dev.md)
- [样式的支持：添加样式，样式文件分离，样式文件压缩](./webpack-react/webpack-style-support.md)
- [样式的进一步处理：CSS 模块化，支持 SCSS, 支持 PostCSS](./webpack-react/webpack-style-support-advance.md)

善后工作

- [分拆 Webpack 配置](./webpack-react/webpack-config-merge.md)
- [配置 eslint 语法检测](./webpack-react/integrate-with-eslint.md)
- [配置源代码查看，调试](./webpack-react/integrate-source-map.md)

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


## Webpack 配置

- [Webpack + React 搭建工程](./webpack-react/webpack-react-project.md)
- [Webpack 基本了解](./webpack-react/webpack-basic.md)
- [Webpack 构建速度优化](./webpack-react/webpack-build-optimize.md)
- [配置动态链接库 Dynamic link library](./dll/dll-basic.md)

## Babel

- [Babel 插件](./babel-tips/babel-plugin.md)
- [Babel Presets](./babel-tips/babel-presets.md)
- [配置 Babel 插件](./babel-tips/configure-babel-plugin.md)

## 杂项

- [🔧 非一级页面刷新返回 404](./webpack-react/refresh-get-404.md)

