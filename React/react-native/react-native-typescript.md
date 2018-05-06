# TypeScript in React Native

在 React Native 中使用 TypeScript

[示例项目](https://github.com/pennyworthit/react-native-typescript)

## Steps

### 搭建 React Native 开发环境

根据 React Native 官网的指引，创建一个最原始的 React Native 项目

[Getting Started](https://facebook.github.io/react-native/docs/getting-started.html)

### 创建项目

```sh
react-native init rnts
cd rnts
```

检查项目是否能正常运行

```sh
react-native run-ios --simulator="iPhone 6s"
```

### 添加 TypeScript 相关的一系列配置

```sh
npm i -D typescript
npm i -D react-native-typescript-transformer
./node_modules/typescript/bin/tsc --init --pretty --jsx react
touch rn-cli.config.js
npm i -D @types/react @types/react-native
```

---

`./node_modules/typescript/bin/tsc --init --pretty --jsx react`

由于这里采用的是本地安装的方法，所以需要在项目中的 `node_modules` 启动 tsc

运行这个命令以后，将会创建 `tsconfig.json` 文件，这个文件大部分配置都是好的，只是，我们需要取消某一行的注释，最后变成一下的样子

`"allowSyntheticDefaultImports": true,`

---

`rn-cli.config.js` 包含了 React Native TypeScript Transformer 的配置，需要在这个原来为空的文件中，添加以下代码

```js
module.exports = {
  getTransformModulePath() {
    return require.resolve("react-native-typescript-transformer");
  },
  getSourceExts() {
    return ["ts", "tsx"];
  }
};
```

### 将项目中的代码迁移到 TypeScript

将 `App.js` 文件重命名为 `App.tsx`, 而 `index.js` 还是使用 `.js` 扩展名

对于所有新建的文件，若文件包含了 JSX, 则文件后缀名使用 `tsx`, 否则，则使用 `ts`

---

若此时重新运行项目，将会得到一个红屏，错误提示为 `object prototype may only be an object or null`

这是由于引入模块时，同时在一行中引入了默认模块和命名模块，修正这个问题，需要修改 `App.tsx` 中的模块引入代码

```
// `-` 表示删除，`+` 表示添加
- import React, { Component } from 'react';
+ import React from 'react';
+ import { Component } from 'react';
```

### 添加 TypeScript 测试配置

## References

- [TypeScript React Native Starter](https://github.com/Microsoft/TypeScript-React-Native-Starter#prerequisites)

