# 使用 TypeScript 编写 npm 包并进行发布

内容包括

- 搭建 TypeScript 开发环境
- 搭建测试环境，使用 TypeScript 编写测试代码
- 将包发布到 npm

## 搭建 TypeScript 开发环境

### 创建源代码文件夹

创建一个文件夹 📁 `lib`, 用于存放包的核心代码

```
.
...
├── lib
├── node_modules
├── package-lock.json
├── package.json
...
```

### 安装依赖

VSCode 对 TypeScript 内置支持，我们只需要在项目中安装对应的开发依赖

- typescript
- tslint 
    - TypeScript 代码风格检查工具
- tslint-config-airbnb
    - tslint 用于检查代码风格的其中一个规范

```sh
npm i -D typescript tslint tslint-config-airbnb
```

### 代码风格检查配置

在根目录下创建 tslint.json 文件

```
.
├── node_modules
├── package-lock.json
├── package.json
...
└── tslint.json
```

并在其中指定风格规范

```json
{
  "extends": "tslint-config-airbnb"
}
```

### TypeScript 编译选项配置

创建一个📁 `config`, 并创建文件 `tsconfig.base.json`, 存放 TypeScript 编译的基本配置

```
.
├── config
│   └── tsconfig.base.json
...
```

而这个基础配置文件，我们可以通过 TypeScript 工具自动生成，默认生成到项目根目录，移动一下就好了

```sh
npx tsc --init
```

---

#### 创建发布的代码的编译配置

前面所创建的 `tsconfig.base.json` 可以作为基本的设置，子配置文件可以通过继承这个文件，根据需要添加或改变设置，避免重复配置

在根目录下，创建 `tsconfig.json`

```
.
├── config
│   └── tsconfig.base.json
...
├── tsconfig.json
...
```

写入以下配置

```json
{
  "extends": "./config/tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./dist"
  },
  "include": [
    "./lib"
  ],
  "exclude": [
    "./test",
    "node_modules"
  ]
}
```

- 配置继承于 `./config/tsconfig.base.json`
- 指定了编译后的代码输出目录为 `./dist`
- 只编译 `./lib` 中的内容
- 忽略测试代码 `./test` 中的内容，以及第三方包 `node_modules` 中的内容

> 1. 为什么要通过继承的方式来编写配置文件？
> 
>   因为在发布的时候，我们只需要源代码编译后的代码。但等下我们也需要使用 TypeScript 来编写测试代码，如果共用一套配置文件的话，发布代码中就会包含测试代码，而测试代码并不需要跟着发布
>   因此，通过继承的做法，编写两套配置文件，就可以根据发布或测试环境，编译出我们所需要的代码
> 
> 1. 通过 `extends` 进行配置文件的继承
> 1. 注意点，`include` 和 `exclude` 配置项是与 `compilerOptions` 同级的

### 修改执行脚本

一切配置结束之后，再修改一下 npm 的 script

```json
...
"scripts": {
    "build": "tsc -p tsconfig.json",
    ...
  },
...
```

当执行 `npm run build` 后，一切正常，那么，在 `./dist/` 下应该就能看到编译后的代码了

## 搭建测试环境，使用 TypeScript 编写测试代码

### 安装依赖

编写测试，我们需要安装测试框架 `mocha` 与断言库 `chai`

而在 TypeScript 环境编写测试，还需要安装定义文件 `@types/mocha`, `@types/chai`

```sh
npm i -D mocha @types/mocha chai @types/chai
```

> 实际上，若想直接将 mocha 在 TypeScript 环境下执行，可以使用 ts-mocha

### 创建测试代码的编译配置

在根目录下，创建 `tsconfig.test.json`

```
.
├── config
│   └── tsconfig.base.json
...
├── tsconfig.json
├── tsconfig.test.json
...
```

写入以下配置

```json
{
  "extends": "./config/tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./test-dist"
  },
  "include": [
    "./lib",
    "./test"
  ]
}
```

- 配置继承于 `./config/tsconfig.base.json`
- 指定了编译后的代码输出目录为 `./test-dist`
- 只编译 `./lib` 以及测试代码 `./test` 中的内容

### 修改执行脚本

一切配置结束之后，再修改一下 npm 的 script

```json
...
"scripts": {
    "build": "tsc -p tsconfig.json",
    "test": "tsc -p tsconfig.test.json && mocha ./test-dist/*"
  },
...
```

当执行 `npm run test` 后，一切正常，那么，在 `./test-dist` 下应该就能看到编译后的代码了，并且在控制台中输出测试信息

## 将包发布到 npm

### 修改 package.json

- 修改入口文件 `main`
- 修改 API 定义文件 `types`
- 添加发布钩子 `prepublish`

```json
...
"main": "index.js",
"types": "index.d.ts",
"scripts": {
  "prepublish": "npm run build",
  "dev": "rm -fr ./dist && tsc -p tsconfig.dev.json",
  "build": "rm -fr ./dist && tsc -p tsconfig.prod.json",
  "test": "ts-mocha -p ./tsconfig.test.json ./test/*"
},
...
```

### 添加忽略文件

当我们将包发到 npm 上时，实际上只需要将编译后的代码发布出去就行，避免将项目中所有的文件发布上去，这样会增大体积

在项目根目录下创建 `.npmignore` 文件

```sh
touch .npmignore
```

### 发布

```sh
npm publish
```

> 发布前，可能需要到 npm 官网上注册账号，并添加到当前执行环境

在每次更改好代码，需要发布前，都应该先执行以下版本更新的命令，而不是手动修改 `package.json` 中的版本信息

```sh
npm version patch
npm version minor
npm version major
```

