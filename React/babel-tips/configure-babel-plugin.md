# 配置 Babel 插件

## 插件位置

当插件发布在 npm 上时，通过 npm 安装在 node_modules 上，此时可以直接写插件名称

```json
{
  "plugins": ["babel-plugin-myPlugin"]
}
```

当插件名称以 `babel-plugin-` 开头时，可以直接使用后部分的名称进行配置，上面的配置等价于

```json
{
  "plugins": ["myPlugin"]
}
```

---

根据本地路径配置

```json
{
  "plugins": ["./node_modules/asdf/plugin"]
}
```

## 插件顺序

Babel 中插件的顺序是有意义的

### 对于直接配置插件

插件的调用顺序从数组第一个开始到最后一个

```json
{
  "plugins": [
    "transform-decorators-legacy",
    "transform-class-properties"
  ]
}
```

运行顺序

1. transform-decorators-legacy
2. transform-class-properties

### 对于配置 Presets

插件的调用顺序从数组最后一个开始到第一个

```json
{
  "presets": [
    "es2015",
    "react",
    "stage-2"
  ]
}
```

运行顺序

1. stage-2
2. react
3. es2015

## 配置插件的选项

```json
{
  "plugins": [
    ["transform-async-to-module-method", {
      "module": "bluebird",
      "method": "coroutine"
    }]
  ]
}
```

插件数组中的每一个元素为一个数组，这个数组的第一个元素为插件名称，第二个元素为插件的选项配置对象

