# Babel 插件

Babel 是一个负责转译 JavaScript 代码的工具，默认有三个阶段

1. 语法解析
1. 转换
1. 输出

但 Babel 本身并没有转译的功能，默认只是输出原有的代码，`code` 并没有发生什么变化

```
code => babel => code
```

要成功转译 JavaScript 代码，一定是需要插件 Plugin 支持

同时，插件分两类

- Syntax
- Transform

## Syntax 插件

此类插件会让 Babel 解析特定的语法，如将 JSX 转换为 JavaScript 的代码

转换 JSX 语法

```json
{
  "parserOpts": {
    "plugins": ["jsx"]
  }
}
```

## Transform 插件

此类插件会转换 JavaScript 特性，如箭头函数转译为普通函数

> 实际上，Transform 类型插件会自动启用对应的 Syntax 插件
> 
> 如 React 的 Transform 插件会自动开启 jsx 的 Syntax 插件

