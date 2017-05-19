# Command

### Mac 上通过 Homebrew 安装 MySQL
`brew install mysql`

### 开启与关闭 MySQL 服务
- `mysql.server start`
- `mysql.server stop`

### 使用 root 登陆 MySQL
`mysql -u root -p`

### 查找所有 MySQL 的进程
`$ ps ax | grep mysql`

### 展示所有数据库
`mysql> show databases`

### 选择使用某数据库
`mysql> use <database name>`

### 展示某数据库中的所有表
`mysql> show tables`

### 查看当前连接状态
`mysql> status`

### 默认存储位置
`/usr/local/var/mysql`


### 查看 MySQL 版本

开启 MySQL 服务

```shell
mysql.server start
```

进入 MySQL 服务

```shell
mysql
```

执行 sql 语句

```sql
select version()
```

### 设置用户密码

设置 root 的密码

```shell
mysqladmin -u root password '密码'
```

但是会出现一条警告：

> mysqladmin: [Warning] Using a password on the command line interface can be insecure.
Warning: Since password will be sent to server in plain text, use ssl connection to ensure password safety.

由于只是本机设置，先忽略

### 卸载 MySQL

通过 Homebrew 安装 MySQL 的，卸载方法

[http://stefan.magnuson.co/articles/osx/reinstalling-mysql-on-osx-with-homebrew/](http://stefan.magnuson.co/articles/osx/reinstalling-mysql-on-osx-with-homebrew/)

### 导出数据库

```shell
mysqldump -u 用户名 -p 数据库名称 > 目标文件.sql
```

