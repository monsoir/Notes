# 自动清理打包文件

当我们修改了业务代码后，运行 `npm run build` 后，会在 `build` 目录下生成新的打包后的文件，但是，旧的打包文件依然存在，并没有自动删除

而之前，我是通过手动在 `build` 命令中添加 `rm build/*` 实现打包前的清理工作

而 Webpack 生态中，[clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin) 插件可以实现这一个功能，并且它提供了更多的选项

## 安装

```sh
npm i -D clean-webpack-plugin
```

## 配置

先修改 package.json 中的 `build` 命令

```diff
"scripts": {
-   "build": "rm build/* && webpack --config config/webpack.config.dev.js",
+   "build": "webpack --config config/webpack.config.dev.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

```diff
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
+ const CleanWebpackPlugin = require('clean-webpack-plugin');

const rootPath = path.join(__dirname, '..');
const publicPath = path.join(rootPath, 'public');

+ const buildPath = path.join(rootPath, 'build');

+ // CleanWebpackPlugin
+ const CleanWebpackPluginOptions = {
+  root: rootPath, // 项目根目录路径
+  verbose: true, // 输出信息到控制台
+ };

module.exports = {
  ...
  plugins: [
    new HtmlWebpackPlugin({
      title: 'An App',
      template: path.join(rootPath, 'public', 'index.html'),
      inject: true,
    }),
+   new CleanWebpackPlugin(['build'], CleanWebpackPluginOptions),
  ],
};
```

按上面的代码配置完成后，执行 `npm run build`, 可以看到 `build` 文件夹首先是被删除，然后重新生成

对于 `clean-webpack-plugin` 插件，其构造函数是又两个参数组成

- 需要清理的路径，传入的是一个数组，即可以有多个清理路径
- 一个选项对象，在本例中，即

    ```js
    {
      root: rootPath, // 项目根目录路径，配置好根路径后，就可以直接在第一个构造函数中传入文件夹名称
      verbose: true, // 输出信息到控制台
    };
    ```

