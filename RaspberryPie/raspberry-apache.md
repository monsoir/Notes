# 树莓派上搭建 Apache 虚拟主机服务

## 安装 Apache

Raspberry Jessie 没有预装 Apache, 需要自己手动安装

```sh
sudo apt-get install apache2
```

## 开始配置虚拟主机服务

1. 进入到 Apache 的配置文件夹

    ```sh
    cd /etc/apache2/
    ```
    
1. 配置需要监听的端口

    ```sh
    sudo vim ports.conf
    ```

    显示内容如下，并添加要监听的端口，比如，这里为 30000
    
    ```
    # If you just change the port or add more ports here, you will likely also
    # have to change the VirtualHost statement in
    # /etc/apache2/sites-enabled/000-default.conf
    
    Listen 80
    Listen 30000 # 添加要监听的端口
    
    <IfModule ssl_module>
            Listen 443
    </IfModule>
    
    <IfModule mod_gnutls.c>
            Listen 443
    </IfModule>
    ```

1. 配置虚拟主机配置文件 vhosts.conf

    vhosts.conf 很有可能是不存在的，如果不存在，就创建一个

    ```sh
    sudo vim sites-enabled/vhosts.conf
    ```
    
    ---
    
    添加内容
    
    ```
    <VirtualHost *:30000>
    ServerName 192.168.1.119
    DocumentRoot "/home/pi/Developer/yaaw"
        <Directory />
            Require all granted
            Options Indexes FollowSymLinks Includes ExecCGI
            AllowOverride none
        </Directory>
    </VirtualHost>
    ```
    
    - 设置映射的 IP 和端口，注意冒号前后不要有空格
    - ServerName 服务器名称，随便来吧，反正也就是自己玩
    - DocumentRoot 配置服务文件的位置，服务器所有文件都应该存放在这里，注意文件夹的权限 755
    - <Directory> 之间的配置也需要添加，否则也是没有权限读取
    
    
1. 链接虚拟文件配置文件

    将上一步创建的 vhosts.conf 软链接到 sites-enabled 文件夹中
    
    ```sh
    ln -s sites-available/vhosts.conf sites-enabled/
    ```

1. 检查 Apache 配置并开启

    ```sh
    sudo apachectl -t # syntax OK 之后重启 Apache 服务
    sudo apachectl restart
    ```

