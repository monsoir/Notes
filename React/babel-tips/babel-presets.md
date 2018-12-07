# Babel Presets

当我们有这样的需求

- 将常用的插件打包成一个集合使用
- 多个插件共享一组配置选项

此时，可以使用 presets, 顾名思义，就是预先设置(pre)好的集合(sets)

## 创建一个 Preset

- 在一个 JavaScript 文件中，导出一个返回对象的函数

```js
module.exports = function() {
  return {
    plugins: [
      "pluginA",
      "pluginB",
      "pluginC",
    ]
  };
}
```

- `plugins` 为组合后的插件集合

```js
module.exports = () => ({
  presets: [
    require("@babel/preset-env"),
  ],
  plugins: [
    [require("@babel/plugin-proposal-class-properties"), { loose: true }],
    require("@babel/plugin-proposal-object-rest-spread"),
  ],
});
```

- `presets` 这个 Preset 同时又可以包含其他 preset

其他的配置规则，与 [配置 Babel 插件](./configure-babel-plugin.md) 相同

