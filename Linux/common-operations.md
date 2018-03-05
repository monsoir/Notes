# Linux 常用操作

## 添加用户

```sh
sudo adduser <username>
```

## 将普通用户添加到 sudoers 中

```sh
sudo usermod -aG sudo <username>

# 或

sudo adduser <username> sudo
```

- `a` 选项非常重要，没有它，将会把用户从其他组中移除
- `G` 用户被分配到的组
- 本质上，这个命令是修改 `/etc/sudoers/` 文件
- 有时候，权限可能需要重启或新建终端才会起效
- 赋予某些用户 root 权限，可以有效隔绝各个用户以及与 root 之间的执行环境，对于某个晋升为 sudoer 的用户来说，执行 root 操作时，使用的是自己的环境，同时输入密码时，也是输入用户的密码，避免暴露 root 的密码

