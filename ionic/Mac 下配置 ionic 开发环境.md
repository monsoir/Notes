# Mac 下配置 ionic 开发环境

总体步骤：
1. 安装 OS X 下的包管理器 [Homebrew](http://brew.sh)
2. 通过 Homebrew 安装 node.js，在 node.js 中将会包含一个包管理器 npm
3. 通过 npm 安装 ionic
4. 通过官网提供的 Demo 检验环境搭建的成果

## 安装 OS X 下的包管理器 Homebrew
- Homebrew 为 OS X 下使用命令行控制的包管理器，为开发人员提供便捷的安装与卸载工具
- 登陆官网，按照官网提示，在终端 Terminal 中输入以下命令，并等待片刻：

	```sh
	/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
	```
	
	![Screen Shot 2016-08-29 at 16.07.11.png](http://ww3.sinaimg.cn/large/801b780agw1f7aoklja5aj21560ekn3f.jpg)

- 按照提示，输入 RETURN 继续安装

	![Screen Shot 2016-08-29 at 16.09.54.png](http://ww3.sinaimg.cn/large/801b780ajw1f7aoma01caj215c08wwhm.jpg)

- 输入管理员密码

	![Screen Shot 2016-08-29 at 16.18.01.png](http://ww2.sinaimg.cn/large/801b780ajw1f7aoulzlogj215g0zk4lh.jpg)

- Homebrew 安装完成

- 通过以下命令，查看 Homebrew 版本，可以检测 Homebrew 的安装状况

	```sh
	# 若成功安装，则会打印 Homebrew 的版本信息
	brew -v
	```
	
	![Screen Shot 2016-08-29 at 16.20.31.png](http://ww4.sinaimg.cn/large/801b780ajw1f7aox6w7qrj20qq02k75e.jpg)

## 通过 Homebrew 安装 node.js

- 键入命令，并稍等片刻

	```sh
	brew install node
	```
	
	![Screen Shot 2016-08-29 at 16.29.06.png](http://ww4.sinaimg.cn/large/801b780agw1f7ap62igyuj21020cudn3.jpg)

出现以上提示后，表示 node.js 安装已经完成

- 键入以下命令，检测 node.js 安装状况

	```sh
	node -v
	```
	
	若安装成功，以上命令将会显示 node.js 的版本号，如
	
	![Screen Shot 2016-08-29 at 16.33.27.png -w300](http://ww1.sinaimg.cn/large/801b780agw1f7apapz8bij20aw01uaa2.jpg)

- 成功安装 node.js 的同时，我们也成功安装了 node.js 的包管理工具和分发工具 npm，键入以下命令，检测 npm 的安装状况

	```sh
	npm -v
	```
	
	若 npm 安装成功，则会输出 npm 的版本号，如
	
	![Screen Shot 2016-08-29 at 16.34.49.png -w300](http://ww4.sinaimg.cn/large/801b780ajw1f7apc51ne8j209m01qmx5.jpg)

## 通过 npm 安装 ionic

- 在终端 Terminal 中输入以下命令，并等待一段时间

	```sh
	npm install -g cordova ionic
	```

- 当在终端上显示出一个树形目录时，则可认为，ionic 已经成功安装，同样可以键入以下命令查看安装状况

	```sh
	ionic -v
	```
	
	若 ionic 成功安装，则会显示 ionic 的版本号，如

	![16:50:09.jpg](http://ww3.sinaimg.cn/large/801b780ajw1f7aprwd6ljj216509841t.jpg)


## 通过官网提供的 Demo 检验环境搭建的成果

- 在终端 Terminal 中键入以下命令，之后等待片刻，由于需要从远端将示例 Demo 获取到本地

	```sh
	ionic start myApp tabs # myApp 为项目的名称，tabs 为项目的样式
	```

- 当示例 Demo 成功获取到本地后，依次输入以下命令

	```sh
	cd myApp # 进入到示例 Demo 的文件夹中
	ionic platform add ios
	ionic build ios
	ionic emulate ios # 在模拟器中运行示例 Demo
	```

- 运行 Demo 的结果

	![demo.gif](http://ww3.sinaimg.cn/large/801b780agw1f7aqlqyre9g20a90j0dnb.gif)


