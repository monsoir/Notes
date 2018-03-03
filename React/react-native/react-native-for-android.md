# Android 上运行 React Native

## 出现的问题

1. 执行 `react-native run-android` 时，提示 `SDK location not found. Define location with sdk.dir in the local.properties file or with an ANDROID_HOME environment variable.`
2. 执行 `react-native run-android` 时，提示错误 `Failed to notify project evaluation listener.`(错误信息部分截取)

## 问题解决

## 问题 1

- 需要手动指定 SDK 路径
- 在 android 目录下，创建新文件 `local.properties`, 填写 `sdk_dir`
     - `sdk.dir = /Users/USER_NAME/Library/Android/sdk`

## 问题 2

- 需要安装 Java 8 (机主机子上安装的是 Java 9)
- 安装 Java 8 并切换 Java 版本，见 [多版本 Java 共存与切换
](../../Homebrew/java-multiple-versions.md)

