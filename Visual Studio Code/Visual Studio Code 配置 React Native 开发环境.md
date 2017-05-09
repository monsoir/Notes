# Visual Studio Code 配置 React Native 开发环境

- [安装 React Native 的 cli](#安装-react-native-的-cli)
- [安装 React Native 开发的插件](#安装-react-native-开发的插件)
	- [React Native Tools](#react-native-tools)
	- [Path Intellisense](#path-intellisense)
- [Debug](#debug)
- [运行到 Android 模拟器中](#运行到-android-模拟器中)

## 安装 React Native 的 cli

[官网指导](https://facebook.github.io/react-native/docs/getting-started.html) 配置好 React Native 的开发环境

## 安装 React Native 开发的插件

### React Native Tools

![React Native Tools](http://ww3.sinaimg.cn/large/006tNbRwgy1feof7wblkqj30co01zzke.jpg)

包含了 React Native 的代码提示，Debug 所需要的工具

### Path Intellisense

![Path Intellisense](http://ww2.sinaimg.cn/large/006tNbRwgy1feof98dfy0j30cv02674d.jpg)

用于提示文件路径，import 模块的时候有用

## Debug

- ⌘ + ⇧ + X 进入到 Debug 控制面板
- 找到 Debug 配置

	![](http://ww2.sinaimg.cn/large/006tNbRwgy1feofc4y1f9j306u01g0sn.jpg)
	
- 添加配置文件

	![](http://ww3.sinaimg.cn/large/006tNbRwgy1feofctomggj306003jt8q.jpg)

- 选择要 Debug 的目标，这里选择 iOS 的

	![](http://ww1.sinaimg.cn/large/006tNbRwgy1feofdvfrwaj30d10agt9r.jpg)

- 按下旁边的运行按钮

	![](http://ww2.sinaimg.cn/large/006tNbRwgy1feoffofid5j301i01jq2s.jpg)


接下来，系统将会启动 packager （若之前没有启动），然后会在模拟器中运行项目

- 项目运行完成之后，要开启 Debug Remote JS

	![](http://ww2.sinaimg.cn/large/006tNbRwgy1feoflef4k6j30ai0jf75h.jpg)

- 在 JavaScript 文件中，打断点，当程序运行到断点处便会停止，实现在 VSCode 中调试，无需再打开浏览器

## 运行到 Android 模拟器中

这里是配合 GenyMotion 的 Android 模拟器

这里假设 Android SDK 等的设置已经完成

### 修改 GenyMotion 上的 adb 路径

在上面的 Add Configuration 一样，添加 Debug Android 选项，直接点击 Debug 运行的话，会出现 `adb command not found` 之类的错误，这里需要设置 GenyMotion 的模拟器设置

![](http://ww4.sinaimg.cn/large/006tNc79ly1ffdxpk0885j31861040xw.jpg)

需要设置 Android SDK 的路径

### 修改用户环境变量


之后还要在 用户目录 `~` 下，修改或新建一个 .bash_profile，来改变当前系统用户的环境变量，添加 Android adb 工具的路径

```shell
export PATH=${PATH}:~/Library/Android/sdk/platform-tools
```

然后，在 `~` 中运行命令，让环境变量的修改生效

```shell
source .bash_profile
```

新建窗口，可使用新的环境变量

同时，可以查看 `$PATH` 来检验环境变量修改是否生效

```shell
echo $PATH
```

### 其他的一些问题

设置完成之后，点击 Debug 运行，会出现警告，之后界面运行不正常

> Debugger and device times had drifted by more than 60s. Please correct this by running adb shell "date date +%m%d%H%M%Y.%S" on your debugger machine

根据提示，还以为是要在 shell 中运行这一命令，但并不起作用

根据 StackOverflow 的提示：[Reference](http://stackoverflow.com/a/39304401/5211544)

需要设置模拟器的时间和时区

设置完成之后，运行正常

