# 树莓派设置静态 IP (WLAN 下)

1. 修改配置文件

	方法 1

	```shell
	sudo vi /etc/network/interfaces
	```

	添加配置

	```
	iface wlan0 inet static
	address 192.168.1.xxx
	netmask 255.255.255.0
	gateway 192.168.1.1
	domain-nameserver 114.114.114.114 # dns 服务器
	```
	
	---

	方法 2

	```shell
	sudo vi /etc/dhcpcd.conf
	```
	
	最后追加

	```
	interface wlan0 # Wi-Fi 连接用 wlan0，网线连接用 eth0
	static ip_address=xxx.xxx.x.xxx/24
	static routers=192.168.1.1
	static domain_name_servers=114.114.114.114
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

