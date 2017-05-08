# 命令使用

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

