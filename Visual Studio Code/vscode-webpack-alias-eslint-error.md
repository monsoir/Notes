# Visual Studio Code 中 eslint 无法对 Webpack 中的 resolve.alias 进行寻址

> 背景
> 
> 当对 [Visual Studio Code 识别 Webpack 中的 resolve.alias](./vscode-webpack-alias.md) 配置完成后，VSCode 能够对 alias 中的路径进行跳转，但发现 eslint 却对 alias 报错！

## 安装插件

需要安装插件 [eslint-import-resolver-webpack](https://github.com/benmosher/eslint-plugin-import) 进行解决

```sh
npm i -D eslint-import-resolver-webpack
```

## 修改 eslint 配置文件

.eslintrc.js

```diff
module.exports = {
  ...
+ "settings": { // 解决 eslint 对 Webpack 中 resolve.alias 无法寻址的问题
+   "import/resolver": {
+     "webpack": {
+       "config": "./config/webpack.config.base.js",
+     },
+   },
+ },
};
```

其中，`config` 为 Webpack 配置文件的路径

## 修改 Webpack 配置文件（optional）

还可能需要更改一下 `resolve.alias` 中的重命名

之前使用的是

```js
'@src': path.join(rootPath, 'src'),
'@img': path.join(rootPath, 'src/img'),
```

但后来发现 eslint 会将 `@src/*` 识别为一个模块，并提议我们使用 npm 进行安装使用，因此最好不要使用 `@**` 的形式进行重命名

但我还是喜欢在重命名的第一个字符使用特殊符号，于是选用了 `$`

```js
$src: path.join(rootPath, 'src'),
$img: path.join(rootPath, 'src/img'),
```

## 修改 jsconfig.js 配置文件

这个文件是提供给 VSCode 进行跳转使用的，经过上一步的修改，当然也需要修改相应的跳转路径

```diff
{
  "compilerOptions": {
    "target": "es6",
    "allowSyntheticDefaultImports": false,
    "jsx": "react",
    "baseUrl": ".",
    "paths": {
-     "@src/*": ["src/*"],
-     "@img/*": ["src/img/*"],
+     "$src/*": ["src/*"],
+     "$img/*": ["src/img/*"],
    }
  },
  "exclude": ["node_modules", "build"],
}
```

## 最后

修改所有组件代码中的引入方式，reload VSCode 或者让 eslint 直接重启，使得配置文件可以被重新读取


