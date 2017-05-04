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


