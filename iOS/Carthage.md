# Carthage 使用方法

## Carthage 使用流程

1. 创建一个文件，名为 Cartfile, 在其中列出项目中需要使用到的 framework
2. 运行 Carthage, 这将会从远端拉取 Cartfile 中列出的项目，并构建对应的 framework
3. 讲构建后的二进制代码 `.framework` 文件拖进 Xcode 项目

> 虽然 Carthage 构建所有需要的依赖并生成对应的二进制 framework, 但用户仍然保持对项目文件结构以及工程构建设置的控制
> 
> Carthage 并不会修改用户的工程文件和构建设置

## 安装 Carthage

3 种方法可供选择

- Installer: 通过下载并运行 Carthage.pkg 文件进行安装
- Homebrew: 使用 Homebrew 安装

    ```sh
    brew update
    
    brew install carthage
    ```
    
    > 若之前安装过了 Carthage 的二进制版本，应先删除 `/Library/Frameworks/CarthageKit.framework`

- 源码：clone 主分支代码，运行 `make install` （这需要 Xcode 8.3, Swift 3.1）


## 将 framework 添加到项目中

**Carthage 只支持动态 framework, 而这只能在 iOS 8 或以上的版本使用(macOS 随意)**

### 对于 iOS, tvOS, watchOS

1. 创建一个文件，名为 Cartfile, 在其中列出项目中需要使用到的 framework
2. 运行 `carthage update`, 这将会拉取所有的依赖到 📁Carthage/Checkouts, 然后构建或下载一个 pre-compiled 的 framework
3. 在项目的 targets -> General -> Linked Frameworks and Libraries 中，将每一个需要的 framework 从 📁Carthae/Build 中拖放到其中
4. targets -> Build Phases -> + -> New Run Script Phase, 创建一个运行脚本

    ```sh
    /usr/local/bin/carthage copy-frameworks
    ```

5. 在 Input Files 中添加要使用到的 framework 的路径

    ```sh
    $(SRCROOT)/Carthage/Build/iOS/XXX.framework
    ```

6. 在 Output Files 中添加 framework 复制的目的路径

    ```sh
    $(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/XXX.framework
    ```
    
    当 output files 配合 input files 一起使用时，Xcode 只会在 input files 发生了改变或 output files 找不到时才会运行脚本。这意味着，当没有使用 Carthage 重新构建 framework 时， dirty build 会运行得更快

脚本的作用：

是应对应用提交到 App Store 时的一个 bug. 这个 bug triggered by universal binaries and ensures that necessary bitcode-related files and dSYMs are copied when archiving.

### 运行一个使用 Carthage 的项目

运行命令

```sh
carthage bootstrap
```

### 更新 framework

当修改了 Cartfile, 或者想将 frameworks 更新到最新版本，运行命令

```sh
carthage upadte xxx
```

或者，只想更新特定的依赖，那就在 update 后面加上它们的名字吧

```sh
carthage update xxx yyy
```

## References

- [原文 👉 https://github.com/Carthage/Carthage](https://github.com/Carthage/Carthage)

