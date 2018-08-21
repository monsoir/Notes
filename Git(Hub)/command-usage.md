# 命令使用

- [基本操作](#基本操作)
- [远程仓库](#远程仓库)
- [分支](#分支)
- [标签](#标签)
- [References](#references)

## 基本操作

### 本地创建一个 Repo

```shell
git init
```

### 添加文件

```shell
# 添加所有文件
git add .
```

### 查看本地仓库的状态

```shell
git status
```

### 设置仓库用户信息

```shell
# 全局设置
git config --global user.name <user name>
git config --global user.email <user email>
git config --list

# 局部设置
git config user.name <user name>
git config user.email <user email>
git config --list
```

### 查看 commit 记录

```shell
git log

# 打印出一行的格式
git log --pretty=oneline
```

### 回退版本

```shell
# 回退到上一个版本
git reset --hard head^

## 回退到上上一个版本
git reset --hard head^^

# 回退到往上100个版本
git reset --hard head^^^^...(100 个 ^)
# 或者
git reset --hard head~100
```

## 远程仓库

### 与远程仓库建立关联

```shell
git remote add origin https://github.com/<who>/<what>.git
```

### 推送至远程仓库

```shell
git push origin master
```

### 修改用户名后，修改本地 URL

```shell
git remote set-url origin <new url>
```

### 查看远程仓库的地址

```shell
git remote -v
```

## 分支

### 创建分支

```shell
# 创建分支，并切换到分支
git checkout -b <分支名称>

# 创建分支
git branch <分支名称>
# 切换分支
git checkout <分支名称>
```

### 查看当前分支

```shell
git branch
```

会列出所有的分支，而当前分支会在其名称前标记 `*`

### 提交修改

在分支上的提交与平时并没有什么区别

```shell
git commit
```

### 合并分支到 master

```shell
git merge <分支名称>
```

---

合并完分支后可以删除分支

```shell
git branch -d <分支名称>
```

## 标签

创建的标签只存储在本地，不会推动到远程，推送到远程需要手动操作

### 打标签

```shell
# 首先需要切换到需要打标签的分支上
...

# 打标签
git tag <标签名> # 如 v1.0
```

### 查看所有标签

```shell
git tag
```

### 为某一次 commit 打标签

```shell
# 找出那一次 commit 的 id
git log --pretty=oneline --abbrev-commit

# 打标签
git tag <标签名> <commit id>
```

### 查看标签信息

```shell
git show <标签名>
```

### 创建带说明的标签

```shell
git tag -a <标签名> -m <说明> <commit id>
```

### 删除标签

```shell
git tag -d <标签名>
```

### 将某个标签推送到远程

```shell
git push origin <标签名>
```

### 一次性将尚未推送的本地标签推送到远程

```shell
git push origin --tags
```

### 删除远程标签

```shell
# 1.先从本地删除
git tag -d <标签名>

# 2.推送到远程
git push origin :refs/tags/<标签名>
```

## References

[http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

