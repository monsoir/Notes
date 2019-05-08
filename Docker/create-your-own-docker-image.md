# 创建自己的 Docker 镜像

## 拉取一个 Ubuntu 镜像

```sh
docker pull ubuntu
```

默认拉取 latest 的版本

## 创建一个容器

```sh
docker run -it --name base ubuntu
```

## 安装内容

- git
- curl, 用于安装最新版本的 Node
- Node v12
- Python 3.x
- Ruby 2.x

### 安装

#### 更新 apt-get 源

```sh
apt-get update
```

#### 安装 git

```sh
apt-get install -y git
```

#### 安装 curl

```sh
apt-get install -y curl
```

#### 安装最新的 Node

```sh
curl -sL https://deb.nodesource.com/setup_12.x | bash -
apt-get install -y nodejs
```

最新的安装方法，可以参考 [nodesource/distributions](https://github.com/nodesource/distributions#installation-instructions)

#### 安装 Python 3.x

安装完 Node 后，发现已经有了 Python2 和 Python3, 不知道是之前存在还是在安装 Node 的过程中顺带安装的

#### 安装 Ruby 2.x

```sh
apt-get install ruby-full
```

```sh
ruby --version
# ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-linux-gnu]
```

### 提交容器生成镜像

```sh
docker commit [container id or container name] [image name:[tag name]]
```

提交完成后，使用 `docker image ls` 可以查看刚才创建的镜像

### 将镜像推送到 Docker Hub

- 先在 [Docker Hub](https://hub.docker.com/) 中自己的账户下，创建一个 Repo, 类似 GitHub 的操作
- 将本地的镜像打标签，标签名与远程仓库的一致

    ```sh
    # 本地的镜像：monsoira:latest
    # 远程仓库的镜像：monsoir/monsoira:1.0
    docker tag monsoira:latest monsoir/monsoira:1.0
    ```
    
- 将上面的本地镜像推送到远程仓库

    ```sh
    # 需要先登陆
    # 此次操作是在 Mac 上进行，而 Mac 上 Docker 有 GUI, 已经在 GUI 中登陆，因此可以直接执行这个命令
    docker push monsoir/monsoira:1.0
    ```


## References

- [Building a Custom Docker Image](https://dev.to/ianknighton/building-a-custom-docker-image-32bp)


