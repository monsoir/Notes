# Mongodb

## 安装

```sh
brew install mongodb
```

## 设置数据写入文件

### 配置

默认配置文件路径：`/usr/local/etc/mongod.conf`

阻塞进程启动：

```sh
# 使用 /usr/local/etc/mongod.conf 配置文件
# 端口 27017
mongod --config /usr/local/etc/mongod.conf --port 27017
```

### 创建用户

默认没有任何用户，需要手动创建用户，并在配置文件中配置启用验证

---

创建管理者账户

```js
// 首先选择用户管理的数据库
use admin

// 键入脚本创建一个用户
// Mongodb 的验证策略是，在哪个数据库创建的用户，就在哪个数据库进行验证
// 因此，等下 admin 这个账户需要在 admin 数据库下进行验证
db.createUser({user: "user_name", pwd: "password", roles: [{role: "userAdminAnyDatabase", db: "admin"}]})
```

---

开启验证，修改配置文件，单独添加

```
...
security:
  authorization: enabled
```

---

重启 mongod 服务后，进行验证

```js
// admin 账号是在 admin 数据库中创建的，因此需要先切换到 admin 数据库
use admin

// 验证
db.auth('user_name', 'password')
```


## 数据库控制命令

- show dbs 显示所有数据库



