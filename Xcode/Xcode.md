# Xcode 问题

### 打开 .xcworkspace 文件（使用了 Cocoapods）时闪退

1. `.xcworkspace` -> Show Package Contents
2. 删除 `xcuserdata` 文件夹

### Xcode 9 全屏模式下使用模拟器

模拟器的菜单需要有 Internal 选项，若没有，需要在系统根目录下创建 AppleInternal 的空文件夹

```sh
sudo mkdir /AppleInternal
```

但很可能出现权限问题

```
mkdir: /AppleInternal: Operation not permitted
```

[👉 解决方法](http://www.barretlee.com/blog/2016/04/06/operation-not-permitted-problem-in-linux-or-unix-system/)




