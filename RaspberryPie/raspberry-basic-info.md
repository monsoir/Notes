# 树莓派一些基本信息

## 系统安装

### 使用开箱即用系统

使用 [NOOBS](https://www.raspberrypi.org/downloads/noobs/)

洗卡软件 [SDFormatter](https://www.sdcard.org/chs/downloads/formatter_4/eula_mac/index.html)

### 烧录系统盘(推荐)

烧录软件 [ApplePi-Baker](https://www.tweaking4all.com/software/macosx-software/macosx-apple-pi-baker/)

系统镜像文件 [RaspberryPi Jessie](https://www.raspberrypi.org/downloads/raspbian/)

## 开启 SSH

系统烧录完成，在 boot 跟目录创建一个名为 SSH 的无扩展名文件

## 树莓派默认用户

pi / raspberry



## 常用命令命令

- 设置系统 root 密码

    ```sh
    sudo passwd root
    ```

- 重启

    ```sh
    sudo reboot
    ```

- 关机

    ```sh
    sudo shutdown -h now
    ```

- ssh 连接

    ```sh
    ssh pi@192.168.1.xxx
    ```

