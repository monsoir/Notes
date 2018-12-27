# React + TypeScript + Jest 使用

使用 React, TypeScript, Jest 进行组件开发

[Demo](https://github.com/pennyworthit/react-ts-jest-demo)

## 目录结构

```
├── config
│   ├── jest.config.js
│   ├── tsconfig.base.json
│   ├── tsconfig.dev.json
│   └── tsconfig.prod.json
├── dist
│   ├── HelloWorld.js
│   └── index.js
├── package-lock.json
├── package.json
├── src
│   └── HelloWorld
│       ├── HelloWorld.tsx
│       ├── __test__
│       │   └── HelloWorld.test.tsx
│       ├── index.d.ts
│       └── index.ts
└── tslint.json
```

- config
    - 存放配置文件，包括 TypeScript 编译配置文件，测试工具 Jest 的配置文件
- dist
    - TypeScript 编译后的 JavaScript 文件
- package.json
- package-lock.json
- src
    - 存放源代码
- tslint.json
    - TypeScript 代码风格校验配置文件

### 配置文件结构

#### tsconfig 配置

本文中 TypeScript 安装在本地项目，因此可以使用以下命令创建一个 `tsconfig.json` 文件

```sh
npx tsc --init
```

此时生成的 tsconfig.json 存放在根目录，为了便于组织，将此文件移动到 config 文件夹中，并重命名为 `tsconfig.base.json`, 意为基础的配置文件，接下来，将对开发环境与生产环境分别再创建配置文件

---

开发环境配置文件 `tsconfig.dev.json`

```json
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "outDir": "../dist"
  },
  "include": [
    "../src"
  ],
  "exclude": [
    "./test",
    "node_modules"
  ],
}
```

生产环境配置文件 `tsconfig.prod.json`

```json
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "outDir": "../dist",
    "removeComments": true,
    "sourceMap": false,
    "declaration": true
  },
  "include": [
    "../src"
  ],
  "exclude": [
    "./test",
    "node_modules"
  ],
}
```

- 可以利用 `extends` 字段来指定父配置文件，即可以共享一些配置项
- 此项目中，开发环境与生产环境的配置区别在于，生产环境关闭了 `sourceMap`, 移除代码注释，生成类型声明文件

#### tslint 配置

```json
{
    "defaultSeverity": "error",
    "extends": [
        "tslint-config-airbnb",
        "tslint-react"
    ],
    "jsRules": {},
    "rules": {},
    "rulesDirectory": []
}
```

同样，tslint 配置文件也可以使用以下命令创建

```sh
npx tslint -i
```

> 需要安装 `npm i -D tslint`

---

其中 `tslint-config-airbnb` 和 `tslint-react` 分别是配置 Airbnb 的代码风格与 TypeScript 在 React 中的风格

> 需要安装 `npm i -D tslint-config-airbnb tslint-react`

#### Jest 配置

- 重点：自定义 tsconfig 的配置

Jest 这个测试框架，可以在配置文件配置使用 TypeScript, 免去手动编译 TypeScript 文件后在进行测试

```js
module.exports = {
  roots: [
    "../src", // jest 扫描的目录
  ],
  transform: {
    "^.+\\.tsx?$": "ts-jest", // 哪些文件需要用 ts-jest 执行
  },
  testRegex: "(/__test__/.*|(\\.|/)(test|spec))\\.tsx?$",
  moduleFileExtensions: [
    'ts',
    'tsx',
    'js',
    'jsx',
    'json',
    'node',
  ],
  globals: {
    "ts-jest": {
      tsConfig: 'config/tsconfig.dev.json',
    },
  },
};
```

> 需要安装 `npm i -D ts-jest @types/jest`

- `roots` 定义 Jest 扫描的目录
- `transform` 使用正则表达式来指明什么文件该使用什么工具进行处理
    - 这里就指定，`ts` 或 `tsx` 文件，使用 ts-jest 进行处理
- `testRegex` 使用正则表达式，指明哪些是测试文件，这里的规则是，匹配 `__test__` 文件夹下的 `.test.tsx` 或 `.spec.tsx` 文件
- `moduleFileExtensions` 指明模块文件的后缀名，Jest 将会通过匹配这些后缀名来找到对应的模块

⚠️ 最重要的

当使用 TypeScript 时， Jest 默认将从项目根目录中寻找 `tsconfig.json` 文件，但由于我们自定义了 `tsconfig.json` 文件，因此，需要告诉 Jest (准确来说是 ts-jest) TypeScript 的编译配置文件路径

从 ts-jest 的文档中可以获知，需要在 `global` -> `ts-jest` -> `tsConfig` 中指定配置文件的位置

### 组件目录结构

源代码的目录如下

```
./src
└── HelloWorld
    ├── HelloWorld.tsx
    ├── __test__
    │   └── HelloWorld.test.tsx
    ├── index.d.ts
    └── index.ts
```

- HelloWorld 为 `HelloWorld` 这个组件的文件夹
- `HelloWorld.tsx` 为组件源代码
- `index.ts` 为组件向外暴露的出口
- `index.d.ts` 定义了组件的类型
- `__test__` 为组件的测试代码

## Jest 测试文件编写

```tsx
import * as React from 'react';

import enzyme, { shallow } from 'enzyme';
import enzymeAdapterReact16 from 'enzyme-adapter-react-16';

import HelloWorld from '../HelloWorld';

enzyme.configure({
  adapter: new enzymeAdapterReact16(),
});

it('<HelloWorld />', () => {
  const hw = shallow(<HelloWorld color="white" />); // 使用 shallow 进行浅渲染
  expect(hw.contains(<h1>Hello World!</h1>)).toBeTruthy();
});

```

需要了解几个工具

- Enzyme
    - Airbnb 为 React 开发的断言库
    - 同时，对于不同版本的 React, 还需要配置不同的 Adapter, 才能使 Enzyme 工作
- enzyme-adapter-react-16
    - Airbnb 提供的，针对 React 16 的 Adapter

## package.json 中 files 字段

`files` 字段用于指定特定文件，这些文件将在 `npm publish` 时推送到 npm 仓库中

## References

- [Jest](https://jestjs.io/en/)
- [Publishing A TypeScript React Component To NPM](https://medium.com/@jchiam/publishing-a-typescript-react-component-to-npm-d3cc15b8d0a2)
- [Configuring React 16 + Jest + Enzyme + Typescript](https://medium.com/@mateuszsokola/configuring-react-16-jest-enzyme-typescript-7122e1a1e6e8)
- [ts-jest Configuration](https://kulshekhar.github.io/ts-jest/user/config/)



