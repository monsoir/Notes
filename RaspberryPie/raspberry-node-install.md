# 在树莓派上安装 Nodejs

执行命令 `node -v`  发现版本还是停留在 v0.10.29，并且使用 `apt-get update` 和 `apt-get upgrade` 还是升级不了，需要通过其他方法升级

```sh
# 查看版本
node -v # v0.10.29
sudo su -

# 卸载原来与 Node 相关的东西
apt-get remove nodered -y
apt-get remove nodejs nodejs-legacy -y
apt-get remove npm -y

curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash - # 这个可以到官网指导页面查看
apt-get install nodejs -y

# 再次查看版本
node -v # v9.2.0
```

- [官网指导](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)
- [Raspberry 论坛](https://www.raspberrypi.org/forums/viewtopic.php?t=140747)

