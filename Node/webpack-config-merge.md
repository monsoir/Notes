# 分拆 Webpack 配置

在之前，我们的 Webpack 配置都是写在同一个文件，需要区分 `development` 与 `production` 环境时，都是需要手动修改 `mode` 属性的值

实际上，这样的效率时有点低的

我们可以将 `development` 与 `production` 环境的配置，共同的部分抽离到一个基础配置文件中，然后对应在 `development` 与 `production` 配置文件中引入基础配置文件，再对各个环境配置独有的选项

## 使用工具进行配置文件的合并

合并配置文件，Webpack 生态中提供了库 [webpack-merge](https://github.com/survivejs/webpack-merge)

安装

```sh
npm i -D webpack-merge
```

## 修改各个环境的配置文件

### 基础配置文件

简单说，基础配置文件中包含的功能有

- 配置入口，出口
- 配置 `js|jsx`, `css|scss` 的 loader
- 配置插件
    - HTML 模版注入
    - 构建前自动清理缓存
    - 导出 CSS 文件

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

const rootPath = path.join(__dirname, '..');
const publicPath = path.join(rootPath, 'public');

const buildPath = path.join(rootPath, 'build');

// CleanWebpackPlugin
const CleanWebpackPluginOptions = {
  root: rootPath,
  verbose: true, // 输出信息到控制台
};

module.exports = {
  entry: path.join(rootPath, 'src/index.js'),
  output: {
    filename: '[name].[hash].js',
    path: buildPath,
    // publicPath,
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/env', '@babel/react'],
          },
        },
        exclude: [
          path.join(rootPath, 'node_modules'),
        ],
      },
      {
        test: /\.css$/,
        use: [
          'style-loader',
          {
            loader: 'css-loader',
            options: {
              modules: true,
              localIdentName: '[local]__[hash:7]',
            },
          },
          'postcss-loader',
        ],
      },
      {
        test: /\.scss$/,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: 'css-loader',
            options: {
              modules: true,
              localIdentName: '[local]__[hash:7]',
            },
          },
          'postcss-loader',
          'sass-loader',
        ],
      }
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'An App',
      template: path.join(rootPath, 'public', 'index.html'),
      inject: true,
    }),
    new CleanWebpackPlugin(['build'], CleanWebpackPluginOptions),
    new MiniCssExtractPlugin({
      filename: 'css/[name].[hash].css',
      chunkFilename: 'css/[id].[hash].css',
    }),
  ],
};
```

### `development` 配置文件

简单来说，包含的功能有

- ...基础配置文件
- 配置 `mode` 为 `development`
- 配置 `devServer`
- 配置热替换

```js
const baseWebpackConfig = require('./webpack.config.base');

const merge = require('webpack-merge');
const webpack = require('webpack');

module.exports = merge(baseWebpackConfig, {
  mode: 'development',
  devServer: {
    port: 8080,
    compress: true,
    historyApiFallback: true,
    hot: true,
    noInfo: false,
    // open: true,
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
  ],
});
```

### `production` 配置文件

简单来说，包含的功能有

- ...基础配置文件
- 配置 `mode` 为 `production`
- 压缩 CSS 代码
- 压缩 JS 代码

```js
const baseWebpackConfig = require('./webpack.config.base');

const merge = require('webpack-merge');
const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');

module.exports = merge(baseWebpackConfig, {
  mode: 'production',
  optimization: {
    minimizer: [
      new UglifyJSPlugin(),
      new OptimizeCssAssetsPlugin(),
    ],
  },
});
```

## 修改脚本命令

修改 `package.json` 中的 `scripts` 内容

- `npm run dev` 时使用 `development` 环境下的配置文件
- `npm run build` 时使用 `production` 环境下的配置文件

```diff
"scripts": {
-   "dev": "webpack-dev-server --inline --progress --config config/webpack.config.dev.js",
-   "build": "webpack --config config/webpack.config.dev.js",
+   "dev": "webpack-dev-server --inline --progress --config config/webpack.config.dev.js",
+   "build": "webpack --config config/webpack.config.prod.js",
    "test": "echo \"Error: no test specified\" && exit 1"
},
```

