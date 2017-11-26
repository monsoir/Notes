# 解决树莓派上 ssh 时终端中文显示乱码问题

## 配置树莓派端

1. 进入到系统的语言配置页面

    ```sh
    sudo raspi-config
    ```

1. 在像 BIOS 界面的界面中依次进行操作

    > 空格操作勾选/取消勾选，tab 跳转到[确定，取消]选择区域

    1. 选择 Localisation Options
    2. 选择 Change Locale
    3. 取消勾选 en_GB.UTF-8 UTF-8，勾选 en_US.UTF-8, zh_CN.UTF-8 UTF-8, zh_CN.GBK GBK

1. 安装中文字体库

    ```sh
    sudo apt-get install ttf-wqy-zenhei
    ```

然而，这一切之后，通过 Mac 的 Terminal ssh 访问树莓派，中文还是显示乱码，但通过 ssh 客户端，如 Termius, 中文显示正常，说明问题已经不在树莓派上了

## 配置 Mac 端

在笔者的 Mac 上，系统语言选择了英文，但是区域还是中国，也许是因为这个问题

通过命令 `locale`, 输出

```sh
LANG=""
LC_COLLATE="en_US.UTF-8"
LC_CTYPE="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_ALL=""
```

LANG 和 LC_ALL 是空的，参考网上各种资料，推测就是这个问题造成的

---

解决方法

    1. 在 `~/.bash_profile` 中添加（有这个文件就追加，没有就创建一个再写入）：

        ```sh
        export LC_ALL=en_US.UTF-8
        export LANG=en_US.UTF-8
        ```
        
    2. 重新加载 `~/.bash_profile`, 使得配置生效

        ```sh
        source ~/.bash_profile
        ```

此时，再次运行命令 `lcoale`, 内容已经发生了变化，ssh 到树莓派时，中文也显示正常

```sh
LANG="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_CTYPE="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_ALL="en_US.UTF-8"
```


