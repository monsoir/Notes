# React Native iOS 打包

- [工程根目录下执行 js 文件打包命令](#工程根目录下执行-js-文件打包命令)
- [添加 JS 打包文件到 iOS 项目下](#添加-js-打包文件到-ios-项目下)
- [修改原生代码中，js 入口文件的读取路径](#修改原生代码中js-入口文件的读取路径)
- [打包命令选项详解](#打包命令选项详解)
- [Reference](#reference)

## 工程根目录下执行 js 文件打包命令

```shell
react-native bundle --entry-file demo/index.js --bundle-output ./ios/bundle/index.ios.jsbundle --platform ios --assets-dest ./ios/bundle --dev false
```

**首先要确保相应的文件夹已经存在，如 📁bundle**

--entry-file JS 文件的总入口 绝对路径 | 相对路径

--bundle-output JS 打包文件的出口

--platform 平台 ios 或者 android

--assets-dest JS 打包文件中，存放资源文件的路径，精确到文件夹名称

--dev 若为 false，JS 打包文件将会被压缩，同时 warning 会停用

---

最后生成在 ios 目录下，生成 📁assets 和 📃index.ios.jsbundle，可能还会有 📃index.ios.jsbundle.meta，但这个不需要

## 添加 JS 打包文件到 iOS 项目下

将 📁assets 和 📃index.ios.jsbundle 添加到项目文件夹下，其中 📁 通过 Create folder references 的方式添加，即添加完成后，图标为蓝色文件夹的那种

## 修改原生代码中，js 入口文件的读取路径

修改 AppDelegate.m 为

```objc
jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"index.ios" withExtension:@"jsbundle"];
```

## 打包命令选项详解

```
react-native bundle

Options:

--entry-file Path to the root JS file, either absolute or relative to JS root [required]

--platform Either "ios" or "android"

--transformer Specify a custom transformer to be used (absolute path) [default: "/Users/babytree-mbp13/projects/xcodeProjects/AwesomeProject/node_modules/react-native/packager/transformer.js"]

--dev If false, warnings are disabled and the bundle is minified [default: true]

--prepack If true, the output bundle will use the Prepack format. [default: false]

--bridge-config File name of a a JSON export of __fbBatchedBridgeConfig. Used by Prepack. Ex. ./bridgeconfig.json

--bundle-output File name where to store the resulting bundle, ex. /tmp/groups.bundle [required]

--bundle-encoding Encoding the bundle should be written in (https://nodejs.org/api/buffer.html#buffer_buffer). [default: "utf8"]

--sourcemap-output File name where to store the sourcemap file for resulting bundle, ex. /tmp/groups.map

--assets-dest Directory name where to store assets referenced in the bundle

--verbose Enables logging [default: false]
```

## Reference

[https://segmentfault.com/a/1190000004189538](https://segmentfault.com/a/1190000004189538)


