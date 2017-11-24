# 与 MySQL 交互

## 开启 MySQL 服务

```sh
mysql.server start
```

## 连接/登陆到 MySQL 服务

```sh
mysql -u <用户名> -p
```

## 命令基础

- 每一条命令使用 `;` 或 `\g` 结束，在碰到结束符前，命令不会发送到服务器执行
- 使用 `\G` 结束的命令，让查询结果**不以**表格形式显示，而是以行的形式显示，避免表格内容过长导致格式错乱
- mysql 命令不区分大小写，但表名区分
- 帮助命令
    - help
    - help contents 列出帮助文档的关键词
    - help Data Manipulation 列出其中一个帮助文档
    - help show databases 列出其中一个命令的使用说明
- 命令输入中途清楚所有输入，使用 `\c` + Enter, 可以直接清空输入，返回到 `mysql >`

## 全新 MySQL 内置表

- information_schema
    - 包含服务器的信息
- mysql
    - 存储用户名，密码，权限
- performance_schema
- sys

## 命令 

### 创建数据库

```mysql
CREATE DATABASE test;
```

或

```mysql
CREATE SCHEMA test;
```

### 删除数据库

```mysql
DROP DATABASE <database name>;
```

### 创建数据库，指定默认字符集，指定数据的排序方式

```mysql
CREATE DATABASE <database name>
CHARACTER SET latin1<char set>
COLLATE latin1_bin;
```

### 创建表

```sql
CREATE TABLE test.books (book_id INT, title TEXT, status INT);
```

### 列出数据库中的所有表

```mysql
SHOW TABLES FROM test;
```

### 设定默认操作的数据库

```mysql
USE <database name>;
```

### 查看数据表的结构

```mysql
DESCRIBE <database name>.<table name>;
```

### 插入数据

假设已经使用了 `USE` 命令设置默认表

```sql
INSERT INTO <table name> VALUES (<value1>, <value2>, ...);
```

一次插入多条数据

```sql
INSERT INTO <table name> VALUES(<value11>, <value12>), (value21,value22), ...;
```

### 更新数据

```sql
UPDATE <table name> SET <field> = <value> WHERE <field> = <value>;
```

### 查看表结构

同时回顾建表时的设置，包括自定义的和系统默认的

```mysql
SHOW CREATE TABLE <table name> \G # 用行的方式来显示
```


