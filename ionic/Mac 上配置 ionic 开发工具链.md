# Mac 上配置 ionic 开发工具链

本文介绍的 ionic 开发环境的工具链为：Apache + Sublime Text 3 + ionic + 浏览器

### Sublime Text 3
- Sublime Text 是被誉为神级的编辑器，而它的神级并不在于对代码的调试等作用，而在于插件化，其下载地址是 [点我](https://www.sublimetext.com/3)。
- 下载 Sublime Text 后，我们会发现它只是一个普通得不能在普通的纯文本编辑器，连代码的调试功能都没有。
	![](http://ww3.sinaimg.cn/large/006y8lVajw1f8pugcju6pj31kw12ngmy.jpg)
	
- 然而，其真正的强大之处就在于此，不带任何冗余的功能，一切的功能由用户通过安装插件定制。当中，一个必不可少的包管理器就必须要安装，它就是 [Package Control (点击即可到达官网)](https://packagecontrol.io/installation)。官网上有完整的安装指南，这里重复一下：

> 此安装方法使用于 Sublime Text 3
> 在 Mac 上的 Sublime Text 3 中，按下快捷键 `ctrl+`` 打开控制台，复制并黏贴一下代码到控制台中，稍等片刻，即可完成安装:
>
```py
import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

- 安装完成后，按下 `cmd + shift + P` 即可打开 Package Control，在输入栏中键入 `install package` 命令后车后
	![-w600](http://ww2.sinaimg.cn/large/006y8lVajw1f8puw8m7ohj318a0e40tx.jpg)
	
- 稍等片刻，等待 Package Control 加载插件库，完成后，在输入栏中键入插件名称，回车后即安装

	![-w600](http://ww3.sinaimg.cn/large/006y8lVajw1f8puxrloaoj30uq0n8mz9.jpg)

- 其中，值得值得推荐的插件有
  - [AngularJS](https://github.com/angular-ui/AngularJS-sublime-package) 用于 AngularJS 的语法提示
  - [SublimeLinter](http://www.sublimelinter.com/en/latest/) 用于检查 JavaScript 语法
  - [Ionic Framework](https://github.com/imsingh/ionic-sublime-plugin) 用于 ionic 语法提示
  - [Side​Bar​Enhancements](https://packagecontrol.io/packages/SideBarEnhancements) 侧边栏增强工具

### ionic

- ionic 的开发环境搭建见第一个微课：Mac 下配置 ionic 开发环境

### Apache

- Sublime Text 不像 WebStorm 一样，可以从编辑器中点击运行项目。然而，我们可以通过 Apache 搭建一个本地的服务器，像访问一般网站一样访问我们的 ionic 项目
- 在 macOS 中，是内置了 Apache，因此，我们只需要学会配置，即可使用


1. 进入到 Apache 的配置文件所在的文件夹
	`cd /etc/apache2`

2. 修改 Apache 配置，修改 httpd.conf 文件
	`sudo vim httpd.conf`

3. 在 httpd.conf 中找到 "#Include /private/etc/apache2/extra/httpd-vhosts.conf"，去掉前面的"＃"，保存并退出。此时，开启了 Apache 的虚拟主机的功能

4. 修改 Apache 虚拟主机配置文件
	`sudo vim extra/httpd-vhosts.conf`

5. 找到 <VirtualHost *:80> 标签，将其中的所有内容注释
	![](http://ww4.sinaimg.cn/large/006y8lVajw1f8pw9snj1dj319k0oate8.jpg)
	
6. 添加以下代码
	![](http://ww2.sinaimg.cn/large/006y8lVajw1f8pwt6rulkj316o0gagpk.jpg)
	其中，DocumentRoot 与 <Directory > 中的路径为项目的路径
	
7. 确保项目的文件夹访问权限为可读写

8. 重启 Apache 服务
	`sudo apachectl restart`
	
9. 此时，即可在浏览器中访问 localhost 或者 127.0.0.1 来访问项目
	![](http://ww3.sinaimg.cn/large/006y8lVajw1f8pww8ajb9j31kw0ynmyr.jpg)

### 浏览器

浏览器可用作前期开发的调试工具，推荐使用的是 Google Chrome 或者 macOS 自带的 Safari


