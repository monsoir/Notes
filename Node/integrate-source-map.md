# 配置源代码查看，调试

当需要在浏览器中调试代码时，需要找到对应的文件，在 Mac 中，我们会在调试模式中按下 `⌘ + p` 并选择对应的文件来打上断点进行调试

然而，以 `Content.jsx` 为例，我们得到的结果是

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvftf3zrj5j312i0mqjz2.jpg)

这并不是我们想要的结果

## 添加 source-map

要在浏览器中打开源代码，需要我们在 Webpack 的开发模式的配置文件中添加一个配置

webpack.config.dev.js

```diff
...

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
+ devtool: 'source-map',
});
```

而后，重启一下服务，在浏览器中打开 `Content.jsx`, 即可得到

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvftdqklplj30oe0l8dj9.jpg)

