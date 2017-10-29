# 将 Node 包发布到 npm

## 注册一个 npm 账号

[👉 地址](https://www.npmjs.com/signup)

## 本地登陆到 npm

运行命令

```sh
npm adduser
```

或

```sh
npm login
```

## 修改 package.json

```js
{
    "name": "<npm 包名>",
    "author": "yourname <me@hello.com> 作者名称",
    "version": "1.0.0 版本号",
    "main": '入口文件.js'
}
```

## 发布

```sh
npm publish
```

### 发布命令行工具

1. 创建 bin 目录，将源代码放进去
2. 在 package.json 中，添加字段 `bin: {"command": "path/to/entry"}`

npm 版本控制规则，语义化版本控制规范（[SemVer](http://semver.org/lang/zh-CN/#语义化版本控制规范semver)）

## 更新包

在项目根目录运行命令，这会更新项目的版本号

```sh
npm version patch
```

随后，再次执行

```sh
npm publish
```

## 错误处理

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fkzjlkd4ivj30m804g750.jpg)

此时，需要查看一下 npm 设置的源，运行命令

```sh
npm config get registry
```

一般，会显示出淘宝的源，此时，我们需要将源切换会官网的源

```sh
npm config set registry http://registry.npmjs.org 
```

两个源

```
淘宝源 https://registry.npm.taobao.org/
npm 源 http://registry.npmjs.org 
```



