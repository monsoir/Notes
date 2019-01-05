# Visual Studio Code 中调试使用 ES6 特性的 Node.js

- [背景](#背景)
- [使用 Babel](#使用-babel)
- [使用 sourcemap](#使用-sourcemap)
- [写一个 Demo](#写一个-demo)
    - [调试](#调试)
- [总结](#总结)

## 背景

虽然说 Node.js 在 v8.5 之后开始支持了 ES6 的特性，具体来说有 import, export 等，但是使用起来还是需要额外的步骤，而这些步骤会导致项目结构发生一点不太协调的变化。

在 Node.js v8.5.0 中，我们可以通过以下步骤来使得 Node.js 直接运行 ES6 的模块特性

1. 使用了模块特性的文件，后缀需要为 `.mjs`
2. 执行脚本时，命令需要添加一个选项 `node --experimental-modules index.js`

然而，以上的步骤，会导致一些瑕疵问题

1. 部分文件名使用了 `.mjs`, 导致了整个项目的 js 文件名可能风格大变，一堆 mjs 文件，看着不舒服
2. 使用了 `.mjs`, 若在 vscode 中使用了 Node.js Modules Intellisense 插件来为监测模块打辅助的话，这是就有点问题了，因为这个插件默认的配置并不支持 `.mjs` 文件，需要在 User Settings 里面对配置进行修改，修改为下面的样子

    ```json
    "node-module-intellisense.fileModuleExtensions": [ 
    ".js", 
    ".jsx", 
    ".ts", 
    ".d.ts", 
    ".tsx", 
    ".vue", 
    ".json", 
    ".mjs" 
    ], 
    ```

3. 我们可能需要在 package.json 对 `scripts` 中的命令进行修改，添加 `--experimental-modules`
4. 总的来说，这其实也是一个方法，但是感觉对项目的侵入性有点大

于是，还有一个办法就是，使用 Babel 来对代码进行转译，并对 vscode 的调试文件 launch.json 进行配置了

## 使用 Babel

使用 Babel 对 js 文件进行转译，转译后的代码会生成在另外一个目录，这里称它为 build, 于是我们可以直接用 `node` 命令对 build 中的 index.js 进行裸运行（不需要添加任何选项），而且一般来说，这会运行的很顺利，前提是我们不需要对代码进行 debug.

说到 Debug, 这里就会产生一个问题了，我们只能在 build 中的文件中打断点，在源文件中打断点可不会产生任何中断的。那要是这样的话，那 debug 还有什么意义呢？

获得了同行大牛的指点，知道了如何正确配置 sourcemap 来获得对源文件的 debug 支持了 [👉 讨论贴](https://www.v2ex.com/t/391485#reply11)

## 使用 sourcemap

使用 sourcemap, 可以在编译代码运行出错后，定位到源文件中出错代码的位置，因为 sourcemap 中包含了需要的信息


## 写一个 Demo

### 安装 Node.js Demo 需要的依赖

```sh
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

上述命令会安装 babel 的最新版本，所安装的依赖版本如下

```js
"devDependencies": {
    "@babel/cli": "^7.2.3",
    "@babel/core": "^7.2.2",
    "@babel/preset-env": "^7.2.3"
}
```

其中，@babel/preset-env 能够足够智能地去转换运行平台中不支持的语法，包括 ES6 中的 `import`

### 为 package.json 添加运行命令

在 package.json 的 `script` 中，添加下面命令

```json
"build": "babel ./src -d ./build --source-maps",
"start": "npm run build && node ./build/index.js",
```

- build 命令，使用 babel, 当前目录中的 📁src 中的代码进行转译，转译后的结果保存在当前目录中的 📁build 中
- run 命令，先运行 build 命令，然后运行 📁build 中的 index.js 文件

### 配置 .babelrc

在 package.json 所在的目录中，创建 📃.babelrc, 并在其中添加配置

```
{
    "presets": [
        "@babel/preset-env",
    ]
}
```

### 添加执行文件

1. 创建 📁src
2. 在 📁src 中，分别添加 imported.js 和 index.js

```js
// imported.js

export default function hello() {
    return 'hello world';
};
```

```js
// index.js
import hello from './imported';
console.log(hello());
```

完成后，项目的目录结构大致如下

![](https://ws2.sinaimg.cn/large/006tNc79gy1fjon5qjcl8j30m408i0t6.jpg)

#### 尝试直接运行

Node 版本：v10.11.0

```sh
node src/index.js
```

上述命令后，可以看到控制台的报错

```
/Users/xxx/Developer/JavaScript/debug-es6-with-babel/src/index.js:1
(function (exports, require, module, __filename, __dirname) { import hello from './imported';
                                                                     ^^^^^

SyntaxError: Unexpected identifier
    at new Script (vm.js:79:7)
    at createScript (vm.js:251:10)
    at Object.runInThisContext (vm.js:303:10)
    at Module._compile (internal/modules/cjs/loader.js:657:28)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:700:10)
    at Module.load (internal/modules/cjs/loader.js:599:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:538:12)
    at Function.Module._load (internal/modules/cjs/loader.js:530:3)
    at Function.Module.runMain (internal/modules/cjs/loader.js:742:12)
    at startup (internal/bootstrap/node.js:279:19)
```

### 对源文件进行编译

执行 package.json 中我们定义的命令

```sh
npm run build
```

可以看到，项目的目录结构发生了一点变化

![](https://ws3.sinaimg.cn/large/006tNc79gy1fjon56q6thj30ma0cmq3m.jpg)


### 运行编译后的代码

运行命令

```sh
npm run start

# 或

node build/index.js
```

结果

```
> debug-es6-with-babel@1.0.0 build /Users/xxx/Developer/JavaScript/debug-es6-with-babel
> babel ./src -d ./build

Successfully compiled 2 files with Babel.
...$ npm run start

> debug-es6-with-babel@1.0.0 start /Users/xxx/Developer/JavaScript/debug-es6-with-babel
> npm run build && node ./build/index.js


> debug-es6-with-babel@1.0.0 build /Users/xxx/Developer/JavaScript/debug-es6-with-babel
> babel ./src -d ./build

Successfully compiled 2 files with Babel.
hello world
```

成功输出了 hello world, 说明转译后的代码运行正常


### 调试

#### 配置 launch.json

launch.json 是 vscode 的 debug 配置文件，当我们按下 `⌘ + ⇧ + D` 的时候，vscode 会自动创建一个这样的文件，位于 .vscode/launch.json

![](https://ws1.sinaimg.cn/large/006tNc79gy1fjoppmaa6wj30mc0ec754.jpg)

launch.json 配置如下

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "program": "${workspaceRoot}/build/index.js"
        },
        {
            "type": "node",
            "request": "attach",
            "name": "Attach",
            "port": 5858
        }
    ]
}
```

其中，program 中的运行文件，改为 📁build 下的 index.js, 即转译后的文件

#### 调试运行

先在 src/imported.js 中打下一个断点

![](https://ws1.sinaimg.cn/large/006tNc79gy1fjordjpco4j30r407imy4.jpg)

启动调试运行后，我们发现程序运行完成，在整个过程中，都没有停留在 src/imported.js 中的断点

#### 设置 sourcemap

断点并没有生效，这是因为 vscode 并未能通过转译后的代码找到源代码，因此我们需要在设置一下，告诉 vscode 使用 sourcemap

修改 launch.json 为

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "program": "${workspaceRoot}/build/index.js",
            "sourceMaps": true,
            "trace": true,
        },
        {
            "type": "node",
            "request": "attach",
            "name": "Attach",
            "port": 5858
        }
    ]
}
```

#### 调试中又一个的问题

💡 以下问题，在 Node v10.11.0, Visual Studio Code Version 1.30.1 下，已经不存在，也许 vscode 团队也在更早版本中修复了这个问题，因此，下面的配置可能是可以忽略的

sourcemap 设置完成之后，我们设置 3 个断点

1. src/index.js

    ![](https://ws2.sinaimg.cn/large/006tKfTcgy1fjwqdkhmkqj31kw03x3yv.jpg)

2. src/imported.js

    ![](https://ws4.sinaimg.cn/large/006tKfTcgy1fjwqev0zznj31kw04sdga.jpg)

3. src/imported2.js 这里新添加了一个文件

    ![](https://ws1.sinaimg.cn/large/006tKfTcgy1fjwqf5sdpsj31kw04agm5.jpg)

##### 情况一

请用全部 3 个断点，进行调试，对每一个断点逐一调试，发现断点都生效了

##### 情况二

禁用第一个断点，启用第二，第三个断点，发现断点并没有生效，甚至报错了

报错信息类似如下

```
Debugging with inspector protocol because Node.js v8.5.0 was detected.
Verbose logs are written to:
/var/folders/m7/ys_3bv0d6yv7hhm1dt0m7y_h0000gn/T/vscode-node-debug2.txt
node --inspect=27982 --debug-brk build/index.js 
Debugger listening on ws://127.0.0.1:27982/50285c8b-333e-45a7-b495-9771b8023f19
Debugger attached.
hello world
hello world again
******** Unhandled error in debug adapter - Unhandled promise rejection: Error: not opened
    at LoggingSocket.send (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/node_modules/ws/lib/WebSocket.js:219:16)
    at LoggingSocket.send (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/node_modules/vscode-chrome-debug-core/out/src/chrome/chromeConnection.js:43:20)
    at Client._sendQueuedRequests (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/node_modules/noice-json-rpc/lib/noice-json-rpc.js:82:30)
    at Client._send (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/node_modules/noice-json-rpc/lib/noice-json-rpc.js:76:14)
    at Promise (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/node_modules/noice-json-rpc/lib/noice-json-rpc.js:100:18)
    at Promise (<anonymous>)
    at Client.call (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/node_modules/noice-json-rpc/lib/noice-json-rpc.js:98:16)
    at Proxy.target.(anonymous function) (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/node_modules/noice-json-rpc/lib/noice-json-rpc.js:140:53)
    at NodeDebugAdapter.<anonymous> (/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/ms-vscode.node-debug2/nod[...]
```

结合情况一的状况，我也猜想可能是因为 imported.js 和 imported2.js 没有加载到，导致了 debug 的时候找不到文件了。但是，猜想归猜想，我又能做什么呢？于是，我在 GitHub 上，向 Visual Studio Code 团队提了一个 [👉 issue](https://github.com/Microsoft/vscode/issues/34615)

经过了他们几天的努力，他们给出了答复

```
The breakpoints aren't hit because vscode doesn't know where to set the breakpoints until the scripts are already loaded. By the time it loads the sourcemaps, the code in those files has already run.

If you set the outFiles property of your launch config to point toward those files on disk, then it should be able to load the sourcemaps ahead of time.

The error is a debug adapter bug
```

猜想翻车了，也对，其实是文件早就运行了，而不是没有加载到

根据他们给出的指引，我们需要稍微修改一下 launch.json 配置为

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "program": "${workspaceRoot}/build/index.js",
            "sourceMaps": true,
            "trace": true,
            "outFiles": [
                "${workspaceRoot}/build/*"
            ]
        },
        {
            "type": "node",
            "request": "attach",
            "name": "Attach",
            "port": 5858
        }
    ]
}
```

添加 `outFiles` 域，值为转译后代码的位置

## 总结

在 vscode 中调试使用 ES6 的 Node.js 步骤如下

1. 安装需要的依赖

    ```sh
    npm install --save-dev @babel/core @babel/cli @babel/preset-env
    ```

2. 添加快捷命令(Optional)

    在 package.json 的 `script` 中，添加下面命令

    ```json
    "build": "babel ./src -d ./build --source-maps",
    "start": "npm run build && node ./build/index.js",
    ```

3. 配置 .babelrc

    ```
    {
        "presets": [
            "@babel/preset-env",
        ]
    }
    ```
    
4. 配置 launch.json

    
    ```json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "type": "node",
                "request": "launch",
                "name": "Launch Program",
                "program": "${workspaceRoot}/build/index.js",
                "sourceMaps": true,
                "trace": true
            },
            {
                "type": "node",
                "request": "attach",
                "name": "Attach",
                "port": 5858
            }
        ]
    }
    ```
    
    ~~需要注意的是 `outFiles` 部分~~

5. 写代码，转译，打断点，调试执行

