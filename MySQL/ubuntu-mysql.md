# Ubuntu 上使用 MySQL

## 安装

```sh
sudo apt-get update
sudo apt-get install mysql-server
```

- 安装过程中会提示设置 root 的密码
- MySQL 安装完成后，会自动启动

## 检查 MySQL 的运行状况

```sh
service mysql status

# 或

netstat -tap | grep mysql
```

## 配置 MySQL 允许远程连接

1. 创建一个用户，假设是 alfred
2. 对用户表数据进行修改，配置 alfred 可以从其余 IP 登录

    ```sql
    update mysql.user set host = '%' where user = 'alfred';
    ```
    
    > MySQL 的权限由用户名+主机来决定，参考 [MySQL 安装与配置](./mysql-instructions/mysql-install-config.md)

3. 修改 MySQL 配置文件
    1. 找到 `/etc/mysql/mysql.conf.d/mysqld.cnf` 文件
    2. 注释 `bind-address = 127.0.0.1`
4. 重启 MySQL `sudo service mysql restart`
5. 远程连接

    ```sh
    mysql -u alfred -h <IP> -P 3306 -p
    ```

> 避免使用 root 账户来设置远程登录


