# 树莓派挂载移动硬盘

## 查看系统的硬盘以及分区

显示设备，如 /dev/sda1

```shell
sudo fdisk -l
```

## 使树莓派支持 exFAT 格式的硬盘

安装 exfat-fuse

```shell
sudo apt-get install exfat-fuse
```

## 创建挂在位置

在 /mnt 或 /media 下创建文件夹

```shell
sudo mkdir /media/Christopher
```

还可以，设置目录的所有人和所有组

```shell
sudo chown pi:pi /media/Christopher
```

## 挂载硬盘

```shell
# Christopher 就是挂载的文件夹
sudo mount -o uid=pi,gid=pi /dev/sda1 /media/Christopher/
```

## 卸载硬盘

```shell
sudo umount /media/Christopher
```

## 设置硬盘开机自动挂载

编辑 /etc/fstab，添加

```shell
/dev/sda1 /media/Christopher 
```


