# Visual Studio Code 配置 React Native 开发环境

[TOC]

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


