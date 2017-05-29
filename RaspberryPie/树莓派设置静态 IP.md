# 树莓派设置静态 IP (WLAN 下)

1. 打开配置文件

	```shell
	sudo vi /etc/network/interfaces
	```

2. 修改配置

	添加配置

	```
	iface wlan0 inet static
	address 192.168.1.xxx
	netmask 255.255.255.0
	gateway 192.168.1.1
	dns-nameserver 114.114.114.114 # dns 服务器
	```
3. 重启

	```shell
	sudo reboot
	```

4. 查看

	```shell
	sudo ifconfig
	```
	
	找到 wlan0 栏，查找 `inet addr: xxx.xxx.x.xxx`

