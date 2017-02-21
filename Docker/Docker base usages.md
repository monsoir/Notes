# Docker 基本命令或用法

使用图形化工具安装后，配置加速器

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


## 获取镜像 (Image) ≈ root 文件系统

- `docker pull [options] [Docker Registry地址]<仓库名>:<标签>
	- Docker Registry地址 -> <域名/IP>[:port]，默认为 Docker Hub
	- 仓库名 -> <用户名>/<软件名>，用户名默认为 library
	- e.g: `docker pull ubuntu:14.04`

## 列出已下载的镜像

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


