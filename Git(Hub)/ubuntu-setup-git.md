# Ubuntu 上搭建 git 服务器

- `lsb_release -a` Ubuntu 16.04.3 LTS

## 安装 git

```sh
sudo apt-get install git
```

## 创建一个 git 账户

使用 GitHub 时，我们通常都能看到这样的地址

```
git@github.com:Monsoir/Notes.git
```

最前面的 git 就是我们将要创建的用户

创建 git 用户

```sh
sudo adduser git
```

由于 git 用户需要暴露出去了，所以安全起见，需要禁用 git 的 shell 登录，仅保留 git-shell 的功能

1. 通过编辑 `/etc/passwd` 文件
    - 找到 `git:x:1001:1001:git,,,:/home/git:/bin/bash`
    - 一般刚新建的话，就是在最后一行了
2. 修改为

    ```sh
    git:x:1001:1001:git,,,:/home/git:/usr/bin/git-shell
    ```

    这样，git 用户虽然禁用了 shell 的操作，但仍可以通过 git-shell 来操作 git

## 服务器上创建 git 仓库

创建文件夹

```sh
mkdir test-project.git
```

初始化仓库

```sh
cd test-project.git
git --bare init
```

- 此时，test-project 会生成若干个文件夹
- `bare` 选项，创建一个裸仓库，没有工作区
- 服务器上的 git 仓库一般以 `.git` 结尾

其实，以上的命令相当于

```sh
git init --bare test-project.git
```

改变仓库的拥有者为 git

```sh
sudo chown -R git:git test-project.git/
```

- `chown` 就是切换拥有拥有者和拥有组的命令
- `-R` 递归操作，意思是把该文件夹及其子文件夹和文件都给办了

可以通过查看操作前后文件夹的拥有者 `ll | grep test`

```
# 前
drwxrwxr-x 2 <创建者用户> <创建者用户组> 4096 Mar  6 21:51 test-project.git/

# 后
drwxrwxr-x 2 git    git    4096 Mar  6 21:51 test-project.git/
```

---

服务端的 git 服务器及一个仓库已经好了

---

## 与客户端对接

客户端创建项目

```sh
mkdir test-project
cd test-project
```

在项目中写点东西

```sh
touch README.md
echo "Hello Git" >> README.md
```

在客户端对项目进行 git 的初始化，提交修改

```sh
# test-project/
git init
git add .
git commit -m "init"
```

本地仓库与远程（服务器）仓库绑定

- 首先我们需要先知道服务器中的仓库地址
    - `<user>@<server_ip>:/path/to/repo.git`
    - 对于例子来说，就是 `git@xxx.xxx.xxx.xxx:/home/.../test-project.git`

```sh
git remote add <upstream name> aUser@xxx.xxx.xxx.xxx:/home/.../test-project.git
```

将本地仓库推送到远程仓库

```sh
git push -u <upstream name> master
```

- `<upstream name>`，我的意思是这个 remote 的名称
    - 一个本地仓库可以有多个远端(upstream)
    - 通过 `git remote -v` 命令可以看到一下格式的信息，当我推送到远程服务器时，就可以在 `push` 后选择 upstream

        ```
        <upstream name>	git@xxx.xxx.xxx.xxx:/home/.../test-project.git (fetch)
        <upstream name>	git@xxx.xxx.xxx.xxx:/home/.../test-project.git (push)
        ```

- `u` 选项，在推送到远程服务器完成后，设置 <upstream name> 为默认的远程服务器，在下一次 push 的时候，就可以省略 `<upstream name>`

## 验证一下

通过将远程服务器的仓库 clone 下来，看内容是否一样

```sh
git clone git@xxx.xxx.xxx.xxx:/home/.../test-project.git
```

## 配置公钥

> 此方法还没成功，现时的结果是不需要输入服务器用户的密码，但需要输入公钥的加密 secret
> 目前的解决方案有：
> [http://blog.csdn.net/dreamstone_xiaoqw/article/details/78355873](http://blog.csdn.net/dreamstone_xiaoqw/article/details/78355873)
> [https://ruby-china.org/topics/14182](https://ruby-china.org/topics/14182)
> [https://ruby-china.org/topics/2693#reply2](https://ruby-china.org/topics/2693#reply2)

但是，我们可以发现一个问题，每次 push 和 clone 的时候，都需要输入 git 的密码，非常繁琐，可以通过配置公钥来解决这个问题

### 获取客户端的公钥

```sh
cat ~/.ssh/id_rsa.pub
```

- 一般用过 GitHub 的机子里面都会有这个文件
- 可以看到一大串无序的字母排列
- 记住是文件 `id_rsa.pub`，用来解密的，不要把 `id_rsa` 这个私钥发出去了

若不存在 `id_rsa.pub` 文件，则需要生成，输入命令，并跟着命令行的提示走

```sh
ssh-keygen
```

### 将客户端的公钥传送到服务器

```sh
echo "<从客户端处复制出来的公钥>" >> ~/.ssh/authorized_keys
```

- `~` 这个用户文件夹指的是 git 用户
- 这一步可能需要暂时启用 git 用户的 shell 交互，否则可能不能写入文件

### 使用命令将公钥传送到服务器

```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub git@<server_ip>
```

- `i` 选项后面接公钥文件


