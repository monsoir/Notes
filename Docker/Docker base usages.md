# Docker 基本命令或用法

使用图形化工具安装后，配置加速器，中国镜像 https://registry.docker-cn.com

![](https://ww1.sinaimg.cn/large/006tKfTcgy1fcydp4j0pcj30af0ejt9h.jpg)

- 查看 Docker 版本或信息
	- `docker --verison`
	- `docker-compose --version`
	- `docker-machine --version`
	- `docker info`

- 使用 Docker 开启 Nginx 服务
	- `docker run -d -p 80:80 --name webserver nginx`
		- 如果没有 nginx 镜像，将会自动下载，下载完成后自动开启
		- 访问 localhost 测试是否为 nginx 的欢迎页面
	- `docker stop webserver`
		- 关闭 nginx 服务
	- `docker rm webserver`

- Docker 与宿主机共享数据
    - `docker run -it --rm -v [宿主机绝对路径]:[容器内路径] [镜像名称] /bin/bash`
    - 进入到的命令行环境可以访问到共享目录
    - 需要现在 Docker 容器中创建一个挂载目录，类似 Linux 中挂载外接设备
    - i,t 选项分别表示，进入交互环境，进入控制台
    - rm 表示，退出容器后，删除容器


## 获取镜像 (Image) ≈ root 文件系统

- `docker pull [options] [Docker Registry地址]<仓库名>:<标签>
	- Docker Registry地址 -> <域名/IP>[:port]，默认为 Docker Hub
	- 仓库名 -> <用户名>/<软件名>，用户名默认为 library
	- e.g: `docker pull ubuntu:14.04`

## 列出已下载的镜像

镜像与容器 ≈ 类与实例

### 全部镜像

- `docker images`

### 虚悬镜像

- 旧镜像与新镜像同名，标签为 `<none>`
- `docker images -f dangling=true`
- 删除虚悬镜像
	- `docker rmi $(docker images -q -f dangling=true)`

### 中间层镜像 (也许会重复利用的)

- `docker images` 只会显示顶层镜像
- `docker images -a` 包括中间层镜像在内的所有镜像

## 删除镜像

- `docker rmi <image name>`


## 常用命令

- 查看安装了的镜像

```sh
docker images
```

- 查看正在运行的容器

    ```sh
    docker container ls
    ```

- 查看所有容器

    ```sh
    docker container ls -a
    ```

- 重命名容器

    ```sh
    docker container rename [container id] [container new name]
    ```

- 启动容器

    ```sh
    docker container start [container id | container name]
    ```

- 挂载已运行的容器

    ```sh
    docker attach [container id | container name]
    ```


