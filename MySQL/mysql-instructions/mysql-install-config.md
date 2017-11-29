# MySQL 安装与配置

## MySQL 安装包自带的几个程序

- mysqld 守护进程，默认监听 3306 端口
- mysqld_safe 运行 mysqld 最常见的方法，自动重启崩溃的守护进程
- mysqlaccess 用于创建用户账号和设置权限
- mysqladmin 命令行的数据库服务器管理工具
- mysqlshow 显示各数据库和各表的信息，查看服务器状态
- mysqldump 到处 dump 文件，备份数据或服务器间复制数据

查看 MySQL 是否运行着，Linux 或 Mac

```sh
ps aux | grep mysql

# -a 显示所有用户正在运行的进程，但会忽略没有命令行终端控制的进程，除非指定 -x 选项
# -u 显示特定用户用户的进程
```

查看 MySQL 是否已安装或查看 MySQL 状态

```sh
mysqladmin -p version status
```

## 安装配置 MySQL

Mac 上安装

```sh
brew install mysql
```

MySQL 主配置文件位置，类 Unix 系统

```
/etc/my.cnf
```

⚠️ 在 Mac 上，使用 Homebrew 安装 MySQL 后，主配置文件位于

```
/usr/local/etc/my.cnf
```

配置文件被分成多个小节，使用 [...] 分隔


```
[mysqld]
# ...

[mysql_safe]
# ...

[mysql.client]
# ...
```

> 可以通过在 [mysqld] 下添加 `log=/var/log/mysql` 来设置日志文件的路径，并激活日志功能

---

Mac 上默认的数据存储路径是

```
/usr/local/var/mysql
```

### 设置 root 初始密码

```sh
mysqladmin -u root -p flush-privileges password 'password'
```

对于联网的机器，不直接输入密码，而是

```sh
mysqladmin -u root -p flush-privileges password

# 之后无回显输入密码
```

对于刚安装的 MySQL, 初始密码是空的，输入旧密码时直接回车，再输入新密码，如 `Root1234`


### MySQL 权限问题

MySQL 中权限由 用户名 + 主机 来决定

- root + locahost 可以做任何事情
- root + % % 是指任何主机，这是不安全的，允许从其他地方通过 root 登陆
- '' + localhost 空用户名 + localhost 这是匿名用户

查看用户的组合

```sh
mysql -u root -p -e "SELECT User, Host FROM mysql.user;"
```

对组合进行修改

```sh
# 对 root + localhost 组合进行密码修改
mysql -u root -p -e "SET PASSWORD FOR 'root'@'locahost' PASSWORD('new password');"

# 对 root + 127.0.0.1 组合进行密码修改
mysql -u root -p -e "SET PASSWORD FOR 'root'@'127.0.0.1' PASSWORD('new password');"

# 删除匿名用户
mysql -u root -p -e "DROP USER 'root'@'%';"
mysql -u root -p -e "DROP USER ''@'localhost';"
```

刷行权限，使密码修改等操作立即刷新

```sh
mysqladmin -u root -p flush-privileges
```

## 创建普通用户

创建用户

```sh
mysql -u root -p -e "GRANT <USAGE> ON *.* TO '<username>'@'localhost' IDENTIFIED BY 'password'"
```

使用

```sh
mysql -u root -p -e "GRANT ALL ON *.* TO 'alfred'@'localhost' IDENTIFIED BY 'Root1234';"
```

用户 alfred 拥有所有基本权限


获知某个用户所拥有的权限

```sh
mysql -u root -p -e "SHOW GRANTS FOR 'alfred'@'localhost'\g"
```

## Mac 上迁移数据库文件存储路径

下面方法试用于使用 Homebrew 安装的 MySQL

### 重要文件路径

数据库文件原存储路径

```
/usr/local/var/mysql
```

数据库配置文件路径

```
/usr/local/etc/my.cnf
```

### 开始迁移

1. 停止 MySQL 服务

    ```sh
    mysql.server stop
    ```

1. 更改配置文件

    ```
    # Default Homebrew MySQL server config
    [mysqld]
    # Only allow connections from localhost
    bind-address = 127.0.0.1
    datadir=/new/data/storage/path # 填写新的路径
    ```

1. 迁移原数据库文件

    将 `/usr/local/var/mysql` 下的文件及文件夹，复制到新的路径下

1. 重启 MySQL 服务

    ```sh
    mysql.server start
    ```

