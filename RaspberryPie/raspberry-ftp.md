# 树莓派上配置 ftp 功能

1. 安装 vsftpd

    ```sh
    sudo apt-get install vsftpd
    ```

2. 配置文件

    ```sh
    sudo vim /etc/vsftpd.conf
    ```
    
    比较主流的几个配置选项
    
    ```
    # 禁止匿名访问
    anonymous_enable=NO
    
    # 允许本地用户访问
    local_enable=YES
    
    # 允许写操作
    write_enable=YES
    
    # 权限掩码，转换后的权限即为 rwxr-wr-w
    umask=022
    ```
    
3. 重启 ftp 服务

    ```sh
    sudo service vsftpd restart
    ```

> ftp 可访问的目录默认为登陆用户的 `/home`

