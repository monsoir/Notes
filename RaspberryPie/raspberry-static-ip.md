# 树莓派设置静态 IP

## 有线网络环境

使用 RaspberryPi Jessie 系统，此系统的设置与网上的很多教程有所出入，原因是这个系统中，引入了 dhcpcd.conf 文件，我们需要针对这个文件进行修改

主要的两点是

1. 不修改 `/etc/network/inferfaces` 文件
2. 而是修改 `/etc/dhcpcd.conf` 文件
3. 重启 `sudo reboot`

修改配置

```config
interface eth0 # eth0 表明有线网络
static ip_address=192.168.1.xxx/24 # 树莓派以太口静态 IP
static routers=192.168.1.xxx # 路由器 IP
static domain_name_servers=xxx.xxx.xxx.xxx # DNS 服务器 IP
```

- [https://raspberrypi.stackexchange.com/questions/37920/how-do-i-set-up-networking-wifi-static-ip-address](https://raspberrypi.stackexchange.com/questions/37920/how-do-i-set-up-networking-wifi-static-ip-address)
- [https://gaomf.cn/2016/10/27/Raspberry_Pi_Static_IP/](https://gaomf.cn/2016/10/27/Raspberry_Pi_Static_IP/)


