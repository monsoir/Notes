# 使用 git hooks 例子

这里大致记录一下一个实际项目中使用 git hook 的过程

## 项目的情况与使用 git hook 的需求

### 项目情况

- 一个使用 nest(Node.js) 编写的代理工具
- 使用 Docker 承载的 MongoDB
- 使用 PM2 进行部署 

### 使用 git hook 的需求

- 将本地代码使用 `git push prod production` 后，服务端自动对更新后的代码进行构建和部署
- 而推送代码和执行，可以使用自己的账号，而不是 git 账号

### 其他

- 我自己的服务器上原本有两个账号，一个是 root, 另一个是平常自己 ssh 上去的账号，假设为 monsoir
- 需要新开一个用户 git, 专门用来处理 Git 服务端的文件

## 过程

### 搭建 Git 服务端仓库

按照 [Ubuntu 上搭建 Git 服务器](./ubuntu-setup-git.md) 这篇的叙述，在服务端创建仓库

关键步骤与命令

```sh
# 创建用户
sudo adduser git

# 禁用 git 的 shell 登陆
...

# 创建仓库
git --bare init [your-repo-name].git

# 改变仓库文件夹及其内容的所有者
sudo chown -R git:git [your-repo-name].git/
```

这里要注意的是：

- 这个步骤中创建的仓库，实际上打开后，可以发现其中是找不到项目的代码。要获得源代码，等下还需要在服务端的另一个地方进行 `clone` 或 `pull` 操作，即在服务器上还需要一个「本地」仓库

### 将自己的账号添加到 git 用户组

在上述的命令中，我们把 `[your-repo-name].git` 仓库的所有者赋予了 `git` 用户组中的用户 `git`, 因此自己的账号 `monsoir` 是没有对此文件夹的操作，需要将 `monsoir` 添加到 `git` 用户组中，并设置 `git` 用户组中的成员对 `[your-repo-name].git` 有读写权限

```sh
# 将用户 monsoir 添加到 git 用户组中
sudo usermod -aG git monsoir

# 查看 monsoir 所属的用户组，可以有多个的
groups monsoir

# 对 git 用户组赋予对 `[your-repo-name].git` 的读写权限
# 一般来说，对于组成员都有读(r)权限
sudo chmod -R g+w /path/to/your-repo-name.git/

# 查看 `[your-repo-name].git` 的权限
ll # 一般来说，应该是长这样额：drwxrwxr-x ...
```

### 在服务器上再创建一个「本地」仓库

之前说过 `[your-repo-name].git` 里面是看不到源代码的，因此我们需要在服务器上另外一个位置另起一个文件夹作为「本地」仓库，这个仓库是可以看到源码的

```sh
# 这里的 clone 后面的路径最好就使用这种了
# 使用 username@host:/path... 这种，可能会出现各种权限问题
git clone /path/to/your-repo-name.git
```

### 在开发机上配置推送地址

弄完了服务器上的代码位置，在配置开发机上的代码推送地址

```sh
# 为本地仓库添加远程地址
git remote add prod monsoir@host:/your-repo-name.git

# 查看已添加的远程地址
git remote -v
```

这里注意的是：

- `monsoir` 这里应该是自己在服务器登陆时候使用的用户名，到时候使用 `git push prod production` 后，执行 hook 时的用户就是 `monsoir` 了，否则可能出现其他权限问题
- `prod` 之后就是服务端的仓库名字了，就不用打那么长了。当我们使用 GitHub 时，执行命令 `git push origin master` 时，也是一样的道理，`master` 其实是我们在 GitHub 上的仓库地址的名字

### 在开发机本地仓库中创建一个发布分支

为了规范一点，采用新开一个分支 `production` 专门用来发布使用

```sh
# 创建分支
git checkout -b production

# 创建完发布分支后，我们先将此分支推送到服务器上，方便等下的步骤
git push prod production

# 之后需要提交代码并发布，也是执行
git push prod production
```

这里需要注意的是

- `production` 这个分支名称，并不是写死在 git 代码中，用来发布项目的特别分支；这个是可以自己自定义的。定义的地方在 hook 脚本中

### 服务端代码对接发布分支

在紧接着的上一步中，使用了 `git push prod production` 推送了一个发布分支；之后，我们还要在服务端的源代码中创建一个分支来对接起来，不然之后的发布工作就找不到分支了

```sh
# 进入到服务端的代码仓库，可以看到源代码的那个
...

# 需要先更新一下，把分支信息也更新下来
git pull

# 在服务器的「本地」仓库中，创建一个同名分支对接发布分支
git checkout -b production origin/production
```

注意的是

- 最后一条命令中的 `production origin/production` 中
    - 前一个 `production` 只是一个代号为，也可以是其他名字，这里为了统一不混淆，就同名 production 好了
    - 后一个 `production` 是必须一样的

### 编写 hook 脚本

从 [Git hooks](./git-hooks.md) 可以知道，hook 有很多种。而现在说的「推送完后自动完成部署」的需求，就需要使用 `post-receive` 的 hook

具体的脚本内容，太长了，就贴在 gist 上吧：

[post receive hook for kvasir-book-query-proxy](https://gist.github.com/Monsoir/5c8b1ba01f1773692b7f841aa119af0e)

不过在编写脚本时，需要注意

- 关键步骤记得使用 `echo`, 帮助 debug
- 每个脚本执行时，其环境都是隶属于某个用户。而执行 git hook 脚本时，其隶属的用户就是隐藏在开发机本地仓库配置的远程中，如 `git remote add prod monsoir@host:/your-repo-name.git`, hook 执行时的环境用户就是 `monsoir`
    - 这一点很重要，不注意到这一点，会出现很多权限的问题
- 对于文件的位置，要使用**绝对路径**
- 无论时前台任务还是后台任务，都有返回值，一般 0 值为成功。最好都根据任务返回值再决定下一步的工作

### 部署 hook 脚本

现在说的 `post-receive` 脚本，是属于服务端的 git 脚本，需要放在 `[your-repo-name].git` 仓库中的 `hooks` 文件夹

> 这里的 `[your-repo-name].git` 仓库，指的是服务器上看不到源代码的仓库

我们可以在这个 `hooks` 文件夹中发现，里面有很多预设的 hook 脚本，但其实都不执行的，像它们后缀名所暗示：sample

能正常工作的 hook 脚本，需要

- 脚本文件名匹配对应阶段 hook 的名字
- 脚本文件名没有后缀

我的做法是

1. 把自己写好的 `post-receive` 脚本跟项目放在一起（不是真的放在一起，而是方便一起管理的那种「一起」）
2. 使用软连接把文件链接到 `[your-repo-name].git` 中的 `hooks` 文件夹中

```sh
# 要将脚本赋予可执行的权限
sudo chmod o+x post-receive

# 我是在开发机上写好了，再传到服务端
# 此时，需要 scp 命令
scp /path/to/post-receive monsoir@host:/path/to/project/post-receive

# 将 hook 脚本软连接到 hooks 文件夹
sudo ln -s /path/to/project/post-receive /path/to/your-repo-name.git/hooks/post-receive
```

之后，当开发机上使用 `git push prod production` 推送代码后，服务端就会执行 `post-receive` 脚本，自动部署工作就在这个时候发生了

这里需要注意的是

- 创建软链接时，最好都使用「绝对路径」
- 在整个过程中，包括 `post-receive` 执行完成前的这段时间，开发机与服务器一直是保持着连接，因此，我们在脚本 `echo` 的内容可以即时显示在终端上



