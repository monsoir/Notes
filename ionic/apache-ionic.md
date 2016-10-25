# Mac 上使用 Apache 服务器搭建 ionic 支撑服务（虚拟主机方法）

1. Mac 自带 Apache，关于 Apache 的基本命令
	- `sudo apachectl -v // 查看内置的Apache版本`
	- `sudo apachectl start // 启动 Apache 服务`
	- `sudo apachectl restart // 重启 Apache 服务`
	- `sudo apachectl stop // 关闭 Apache 服务`
	- `sudo apachectl -t  // 检查 Apache 配置是否正确`

2. 修改 Apache 配置文件
	- 打开终端 Terminal，键入命令 `sudo vim /etc/apache2/httpd.conf`，打开 Apache 配置文件
	- 查找 `#Include /private/etc/apache2/extra/httpd-vhosts.conf` 并将 `#` 删除以去除注释，启用
	- 查找 `#LoadModule log_config_module libexec/apache2/mod_log_config.so`，删除 `#` 删除注释，非必要

3. 配置虚拟主机文件
	- Terminal 中键入命令 `sudo vim /etc/apache2/extra/httpd-vhosts.conf`，打开虚拟主机配置文件
	- 找到模版，并在每一行行首添加 `#` 以注释，留着参考用，这两个虚拟主机不存在的

	```
	<VirtualHost *:80>
	    ServerAdmin webmaster@dummy-host.example.com
	    DocumentRoot "/usr/docs/dummy-host.example.com"
	    ServerName dummy-host.example.com
	    ErrorLog "/private/var/log/apache2/dummy-host.example.com-error_log"
	    CustomLog "/private/var/log/apache2/dummy-host.example.com-access_log" common
	</VirtualHost>
	 
	<VirtualHost *:80>
	    ServerAdmin webmaster@dummy-host2.example.com
	    DocumentRoot "/usr/docs/dummy-host2.example.com"
	    ServerName dummy-host2.example.com
	    ErrorLog "/private/var/log/apache2/dummy-host2.example.com-error_log"
	    CustomLog "/private/var/log/apache2/dummy-host2.example.com-access_log" common
	</VirtualHost>
	```

	- 以下这个是部分字段的说明：

	```
	<VirtualHost *:80>                           # Change the 80 to the number Apache2 "Listen"s on. In my case, 8080
	    ServerName SERVER-ADDRESS                # E.g. mywebsite.local
	    ServerAlias WWW.SERVER-ADDRESS           # E.g. www.mywebsite.local
	    DocumentRoot " SERVER-FILE-ROOT "        # E.g. "Users/user-name/Sites"
	        <Directory />                        # This should be a full path, though
	            Require all granted              # Required for permission errors
	            Options Indexes FollowSymLinks Includes ExecCGI
	            AllowOverride none #(or All)
	        </Directory>
	</VirtualHost>
	```

4. 修改完毕后
	- Terminal 中运行命令 `sudo apachectl -t` 可检查修改是否有错误
	- 检查上述配置虚拟文件中的 `DocumentRoot` 每一层的文件夹，其权限需设置为对所有人为 `Read Only`，否则访问时将会出现权限问题
		- 修改权限的方法有二：
			1. Terminal 中运行 `sudo chmod o+r <需修改权限的文件路径或文件名字>`
			2. 选中需修改权限的文件或文件夹，`⌘+I`，在最下面的 Sharing & Permissions 一栏中，everyone 对应的权限选择 Read Only

5. Terminal 中运行 `sudo vim /etc/hosts` 修改 hosts 文件，将 localhost 重定向值 虚拟主机的 `ServerName` 字段的值
6. 重启 Apache 服务或启动 Apache 服务


参考:
1.http://stackoverflow.com/questions/18739764/how-to-set-up-a-virtual-host-on-apache-2-4-4-mac-nix/18739765#18739765
2.http://www.cnblogs.com/snandy/archive/2012/11/13/2765381.html

# 复原 Apache 初始界面
- 在初始状态下，启动 Apache 服务后，访问 localhost/127.0.0.1 返回的页面应该为 `It works!` 页面
- 复原初始状态，可以
	1. 重新注释 httpd.conf 中的 `Include /private/etc/apache2/extra/httpd-vhosts.conf`
	2. 清空 DNS 缓存
	3. 重启 Apache 服务

### Clear the local DNS cache

`dscacheutil -flushcache`

