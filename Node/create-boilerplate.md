# 构建简单的脚手架 React

- [构建命令行工具](#构建命令行工具)

综合多篇构建前端脚手架的文章，得知构建脚手架的大致思路是

1. 使用 Node.js 构建命令行工具
2. 构建前端模版，即项目目录结构
3. 在命令行工具中，结合用户的输入，挑选合适的模版，并将模版从远程仓库中拉取到本地，常用的远程仓库为 GitHub

---

由于集中于 React 的技术栈，因此，命令行工具只会提供 React 框架的挑选，但提供 JavaScript 或 TypeScript 两种语言的挑选

## 构建命令行工具

使用 [commander](https://github.com/tj/commander.js)

```js
// /bin/monsoireact.js

#!/usr/local/bin/node
const program = require('commander');

program
  .version(require('../package').version, '-v, --version') // 设置版本信息

program
  .command('init') // 设置 init 命令
  .description('init a project') // 命令描述
  .option('-t, --type [type]', 'JavaScript type: js or ts', 'js') // 命令选项
  .action(({ type }) => {
    require('../command/init')(type);
  }) // 命令指令逻辑

program.parse(process.argv)
```

使用方法

```sh
monsoireact -v # 版本信息
monsoireact init -t ts // 使用命令及其选项
```

测试使用

1. 在 `package.json` 中添加配置

    ```json
    "bin": {
      "monsoireact": "./bin/monsoireact.js"
    },
    ```

2. 将程序挂载在全局范围

    ```sh
    # 在项目根目录中执行
    npm link
    ```

> 对于 action 的链式调用，如何获取用户的输入？

改造 action 的调用方式

```js
program
  .command('init')
  .description('init a project')
  .option('-t, --type [type]', 'JavaScript type: js or ts', 'js')
  .action((args) => {
    // require('../command/init')(type);
    console.log(args);
  })
```

运行 `monsoireact init -t ts`, 得出如下输出

```
Command {
  commands: [],
  options:
   [ Option {
       flags: '-t, --type [type]',
       required: false,
       optional: true,
       bool: true,
       short: '-t',
       long: '--type',
       description: 'JavaScript type: js or ts',
       defaultValue: 'js' } ],
  _execs: {},
  _allowUnknownOption: false,
  _args: [],
  _name: 'init',
  _noHelp: false,
  parent:
   Command {
     commands: [ [Circular] ],
     options: [ [Object] ],
     _execs: {},
     _allowUnknownOption: false,
     _args: [],
     _name: 'monsoireact',
     Command: { [Function: Command] super_: [Object] },
     Option: [Function: Option],
     _version: '1.0.0',
     _versionOptionName: 'version',
     _events:
Catherine:create-monsoireact christopher$ git status
fatal: Not a git repository (or any of the parent directories): .git
Catherine:create-monsoireact christopher$ git init
Initialized empty Git repository in /Users/christopher/Developer/React/create-monsoireact/.git/
Catherine:create-monsoireact christopher$ monsoireact init -t ts
Command {
  commands: [],
  options:
   [ Option {
       flags: '-t, --type [type]',
       required: false,
       optional: true,
       bool: true,
       short: '-t',
       long: '--type',
       description: 'JavaScript type: js or ts',
       defaultValue: 'js' } ],
  _execs: {},
  _allowUnknownOption: false,
  _args: [],
  _name: 'init',
  _noHelp: false,
  parent:
   Command {
     commands: [ [Circular] ],
     options: [ [Object] ],
     _execs: {},
     _allowUnknownOption: false,
     _args: [],
     _name: 'monsoireact',
     Command: { [Function: Command] super_: [Object] },
     Option: [Function: Option],
     _version: '1.0.0',
     _versionOptionName: 'version',
     _events:
      { 'option:version': [Function],
        'command:init': [Function: listener] },
     _eventsCount: 2,
     rawArgs:
      [ '/usr/local/Cellar/node/8.9.1/bin/node',
        '/usr/local/bin/monsoireact',
        'init',
        '-t',
        'ts' ],
     args: [ [Circular] ] },
  _description: 'init a project',
  _argsDescription: undefined,
  type: 'ts',
  _events: { 'option:type': [Function] },
  _eventsCount: 1 }
```

可以找出

```
type: 'ts',
```

通过对输入参数的析构，可以获取到我们输入的 `type`, 即

```js
program
  .command('init')
  .description('init a project')
  .option('-t, --type [type]', 'JavaScript type: js or ts', 'js')
  .action(({ type }) => {
    require('../command/init')(type);
  })
```

而 `'../command/init'` 则是我们在 command 文件夹下的 init.js 文件，当中定义了一个接收 type 参数的函数

```js
const init = (type) => {
  console.log(type);
}

module.exports = init;
```



