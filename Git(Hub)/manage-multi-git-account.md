# 管理多个 Git 账号

> 背景
> 
> 如何在电脑上（同一个用户账户）中设置管理多个 git 账户，还要能免密连接那种？
> 比如，我个人有两个 GitHub 账户，或者，我有一个 GitHub 账户，一个 GitLab 账户，如何方便地在两个账户之间无缝切换呢？

下面假设电脑上已经存在了一个常用的 git 账号，其信息如下

- 用户名和邮箱名都为 `accountA@xxx.com`
- 私钥文件和公钥文件分别为 `accountA_rsa` 与 `accountA_rsa.pub`

下面，开始创建另一个 git 账号，其用户名和邮箱名为 `accountB@xxx.com`

## 创建钥匙文件

```sh
ssh-keygen -t rsa -C "accountB@xxx.com"
```

接下来，会要求我们输入

- 存放钥匙文件的位置，一般为 `~/.ssh/accountB_rsa`
- 访问文件的密码，可以省略

最后，这里会在 `~/.ssh/` 下生成 `accountB_rsa` 与 `accountB_rsa.pub` 两个文件

## 配置免密登陆

一般来说，平台会提供一个界面让用户来提供公钥内容，我们只需要将公钥内容复制粘贴到相应的位置即可

复制公钥内容

```sh
# 不要复制错了私钥文件内容，正确的应该是带 .pub 后缀名
pbcopy < ~/.ssh/accountB_rsa.pub
```

## 配置好对应的账号与仓库

做好上面的步骤后，我们还需要将 git 账号添加到系统的 ssh 工具中，可以使用命令的方式添加

但是，还有使用配置文件的方式添加，个人感觉配置文件的方式比较灵活，直观，因此这里只讨论配置文件的方式

在 `~/.ssh/` 文件夹下，创建文件 `config`, 即 `~/.ssh/config`

并添加以下内容

```
# account 1 一个块代表一个平台中的一个账号
Host github
HostName github.com
User accountA@xxx.com
IdentityFile ~/.ssh/accountA_rsa

# account 2
Host gitlab.(yyy).com
HostName gitlab.(yyy).com
User accountB@xxx.com
IdentityFile ~/.ssh/accountB_rsa

...
```

注意的是，`IdentityFile` 需要对应好各个账号的私钥文件

## 更改某些仓库的用户名

```sh
git config user.name "accountB@xxx.com"
git config user.email "accountB@xxx.com"
```

## 测试

```sh
ssh -T git@xxx.com
```

如果在上面的步骤中，生成私钥文件是输入了密码，那在连接的时候也需要输入同样的密码

最后，连接成功，就会出现成功连接，欢迎的字样

## 补充

有时候，进行连接时会报出错误

```
git@github.com: Permission denied (publickey).
```

此时，我们还是需要将私钥文件添加到 ssh-key 中

```sh
ssh-add path/to/ssh/private-key/file
```

可以通过以下命令查看添加了哪些撕咬文件

```sh
ssh-add -l
```

