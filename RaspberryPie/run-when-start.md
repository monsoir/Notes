# 树莓派创建与卸载开机自启脚本

树莓派本身比较接近 Ubuntu 系统，因此，搜索设置的时候，可以参考 Ubuntu 的

## 创建开机自启脚本

1. 创建一个脚本，比如一个开机后自动挂载外接硬盘

    mount_them.sh
    
    ```sh
    #!/bin/sh
    
    sudo mount -o uid=pi,gid=pi /dev/sda1 /home/pi/extended/Maxtor
    sudo mount -o uid=pi,gid=pi /dev/sdb2 /home/pi/extended/Repository
    ```

1. 赋予脚本可运行的权限

    刚创建的文件并没有可运行的权限，需要我们手动添加
    
    ```sh
    sudo chmod 755 /home/pi/shells/mount_them.sh
    ```

1. 创建软连接

    树莓派本身的开机自启动脚本存放在 /etc/init.d/ 下，因此我们需要将 mount_them.sh 放置在此文件夹下
    
    放置有两种方法
    
    1. 直接复制
    2. 创建软连接
    
    这里选择第二种方式
    
    ```sh
    sudo ln -s /home/pi/shells/mount_them.sh /etc/init.d/
    ```
    
    **注意要使用绝对路径**

1. 更新启动配置

    ```sh
    cd /etc/init.d
    sudo update-rc.d mount_them.sh defaults 400
    ```
    
    这里最后的 400 是脚本的启动顺序号，这里就让他尽量在最后执行了，可以根据需要设置


## 卸载开机自启脚本

```sh
cd /etc/init.d
sudo update-rc.d -f <卸载的脚本名称 mount_them.sh> remove
```


