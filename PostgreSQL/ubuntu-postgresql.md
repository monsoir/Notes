# Ubuntu 上使用 PostgreSQL

## 安装

- Ubuntu 默认的仓库中包含 Postgres 包，可以简单地通过 `apt` 命令进行安装

```sh
sudo apt-get update # 先刷新包索引
sudo apt-get install postgresql postgresql-contrib
```

`postgresql-contrib` 包含了一些额外的工具和功能

## 使用 PostgreSQL

### 角色

- PostgreSQL 使用一种叫 **角色** 的概念来进行用户认证
    - 类似 Unix-sytle 的账户，但使用的是角色，而不是用户和组
- 安装完成后，会自动生成一个用户 `postgres`, 并拥有默认的角色

#### 切换 PostgreSQL 角色

切换当前用户

```sh
sudo -i -u postgres
```

进入 PostgreSQL 命令行交互

```sh
psql
```

退出 PostgreSQL 命令行交互

```
postgres=# \q
```

#### 直接登入 PostgreSQL

不切换系统用户下以其他用户角色进入 PostgreSQL

```sh
sudo -u postgres psql
```

#### 创建角色

```sh
sudo -u postgres createuser --interactive
```

- `interactive` 将会进入一种选择交互

### 创建数据库

createdb <database name>

### 连接数据库

```sh
psql
```

- 默认地，会使用系统当前的用户来作为 PostgreSQL 用户来连接到数据库

```sh
psql -d <another database name>
```

- 一开始就选择连接的数据库

### 查看当前连接到数据库的信息

```psql
\conninfo
```

## 卸载

先查看 PostgreSQL 都有安装了哪些包

```sh
dpkg -l | grep postgres
```

卸载

```sh
sudo apt-get --purge remove ****
```

- `purge` 移除包及其配置文件
- `remove` 移除包

## References

- [How To Install and Use PostgreSQL on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)

