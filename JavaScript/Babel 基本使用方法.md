# Babel 基本使用方法

## Babel 的配置文件 .babelrc

.babelrc 存放在项目的根目录，用来设置转码规则和插件

```json
{
	"presents": [],
	"plugins": []
}
```

presets 用来设定转码规则，如

```shell
# ES2015
npm install -D babel-preset-es2015

# react
npm install -D babel-preset-react

# react-native
npm install -D babel-preset-react-native
```

> 多个 preset 使用 `,` 隔开

## 使用命令行转码 babel-cli

Babel 命令行工具，babel-cli 安装

```shell
npm install (-g) babel-cli
```

### 基本使用方法

- 转码结果输出到标准输出

	```shell
	babel example.js
	
	babel <源文件>
	```
	
- 转码结果写入一个文件

	```shell
	babel example.js -o compiled.js
	
	babel <源文件> -o <目标文件>
	```

- 整个目录进行转码

	```shell
	babel src -d lib
	
	babel <源目录> -d <目标目录>
	```

## babel-node

babel-cli 自带的命令，提供支持 ES6 的环境，可以直接运行 ES6 代码

## babel-register

- babel-register 模块改写 `require` 命令，加上一个 钩子
- 每当使用 require 加载 `.js`, `.jsx`, `.es`, `.es6` 后缀名的文件时，会先用 Babel 进行转码
- 只适合在开发环境中使用
- 安装在开发依赖中

## babel-core

- 某些代码需要调用 Babel API 来进行转码，需要使用 babel-core 模块
- 安装在正常依赖

## babel-polyfill

- Babel 默认只转换新的的 JavaScript 语法，而不转换新的 API
- 若需要让新的 API 运行，必须使用 babel-polyfill
- 安装在正常依赖

## References

- [http://www.ruanyifeng.com/blog/2016/01/babel.html](http://www.ruanyifeng.com/blog/2016/01/babel.html)




