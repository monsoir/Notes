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

## 将用户添加到某个用户组

```sh
sudo usermod -aG <group-name> <user-name>
```

## 将用户从某个用户组中移除

```sh
sudo deluser <username> <group-name>
```

## 远程文件传输

```sh
scp /path/to/local/file username@host:/path/to/remote/location
```

## 创建软链接

```sh
ln -s /path/to/file /path/to/symbolic
```

路径最好使用「绝对路径」

## 查看用户所属的用户组

```sh
groups <username>
```

## 在用户 A 中临时使用用户 B 的身份执行命令

```sh
su -m <user to switch to> -c <command which does not need interaction>
```

需要注意

- `-c` 后面跟着的命令，必须是不依靠 tty 就可以执行的命令
- 这个命令执行在终端手动执行，不能写在脚本中执行，否则抛出错误

    ```
    'su' command in Docker returns 'must be run from terminal'
    ```

