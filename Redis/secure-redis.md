# Redis 安全

## 为 Redis 设置账户

在 Mac 上，Redis 的配置文件位于 `/usr/local/etc/redis.conf`

Redis 默认不需要账号密码登录，为 Redis 设置账号密码登录，需要修改配置文件，搜索并将以下配置

```
# requirepass foobared
```

修改为

```
requirepass <your-password>
```

重启 Redis 服务

命令行进入 Redis 控制终端时，需要加上密码选项

```
-a <your-password>
```

> 当没有使用密码的情况下，进入 Redis 控制台是允许的，只是各种操作都没有权限


