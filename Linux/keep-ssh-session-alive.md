# 保持 ssh 连接

在客户端使用 ssh 连接到服务器后，如果长时间没有操作，连接会自动断开并给你返回 Broken pipe 的信息

如何保持 ssh 的连接？

可以从客户端或者服务端入手解决，但本质上还是使用心跳包来刺激连接

## 服务端

在服务端，在文件 `/etc/ssh/sshd_config` 中添加配置，需要管理员权限

```
ClientAliveInterval 60
ClientAliveCountMax 1
```

- ssh 服务器每 `ClientAliveInterval` 秒发送一个信号到客户端，并等待客户端响应
- 若客户端没有响应，则记录下没响应的次数，当次数超过 `ClientAliveCountMax` 后，则断开连接

最后，重启 ssh 服务

```sh
sudo service ssh restart
```

## 客户端

在客户端，在文件 `/etc/ssh/sshd_config` 中添加配置

```
TCPKeepAlive yes
ServerAliveInterval 300
ServerAliveCountMax 3
```

- ssh 客户端根据 `TCPKeepAlive` 决定是否发送心跳包保持连接
- 客户端每隔 `ServerAliveInterval` 秒向服务器发送数据包，表示要保持连接
- 若服务端没有响应，则记录下没响应的次数，当次数超过 `ServerAliveCountMax` 后，则断开连接

