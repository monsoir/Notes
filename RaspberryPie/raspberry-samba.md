# 树莓派配置 Samba 共享

1. 安装 Samba

    ```sh
    sudo apt-gt samba
    ```
    
1. Samba 配置文件

    ```sh
    sudo vim /etc/samba/smb.conf
    ```
    
    配置可匿名访问的共享
    
    ```
    [share] # 访问路径
    comment = 'share for work'
    path = /home/pi/extended/Repository/Media/电影 # 映射的路径
    public = yes
    guest ok = yes
    create mask 0666
    directory mask = 0666
    force user = pi
    force group = pi
    browseable = yes
    available = yes
    ```

1. 重启 Samba 服务

    ```sh
    sudo samba restart
    ```

1. 访问

    1. Samba 服务重启完成后，可以在 Mac 上通过 `⌘ + K` 来输入 `smb://xxx.xxx.xxx.xxx/share` 来访问共享资源
    2. 通过小米盒子的高清播放器中的共享资源访问

