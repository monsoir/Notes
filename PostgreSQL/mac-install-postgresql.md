# Mac 上安装配置 PostgreSQL

通过 Homebrew 安装

```sh
brew install postgresql
```

创建数据库

```sh
initdb /Users/christopher/Developer/postgresql/ -E utf8
```

> `/Users/USER_NAME/Developer/postgresql/` 是自定义路径，可以设定数据库的路径
> `-E utf8` 设定数据库编码为 UTF-8
> 默认地，安装完 PostgreSQL, 会在 `/usr/local/var/postgres` 中创建一个数据库

启动 PostgreSQL

```sh
pg_ctl -D /Users/USER_NAME/Developer/postgresql/ -l logfile start

# 或

pg_ctl -D /Users/USER_NAME/Developer/postgresql/ -l /Users/USER_NAME/Developer/postgresql/logfile start
```

关闭 PostgreSQL

```sh
pg_ctl -D /Users/USER_NAME/Developer/postgresql/ stop -s -m fast
```

创建一个用户

```sh
createuser <username> -P
```

创建一个数据库

```sh
createdb <database name> -O <username> -E UTF8 -e
```

- database name 数据库名称
- username 数据库的拥有者，默认的操作账户为系统当前的账户
- UTF8 设置数据库的编码
- `-e` 回显操作信息

## PostgreSQL 的 GUI 工具

- Navicat for PosgreSQL
- SQLPro for Postgres

