# Docker cheat sheet

## 查看 Docker 信息

```sh
docker version
```

```sh
docker info
```

## Linux 上给 Docker 授权

```sh
sudo usermod -aG docker [user]
```

## 概念

### image

- image 文件，应用程序及其依赖的模版
- 同一个 image 文件可以生成多个同时运行的容器实例
- image 文件可以通过继承来扩展 image 的配置

### container

容器开启后会存在两种文件

- image 模版文件
- 容器文件

其中，容器关闭后，容器文件不会被删除，只是容器的运行被停止而已

## Linux 上开启 Docker 服务

```sh
docker service docker start
```

```sh
docker systemctl start docker
```

## image 相关的命令

查看 image 文件

```sh
docker image ls
```

删除 image 文件

```sh
docker image rm [image name]
```

拉取 image 文件，image group name 默认为 library

```sh
docker image pull [image group name]/[image name]
```

## container 相关命令

### 查看

查看正在运行的容器

```sh
docker container ls
```

查看所有容器

```sh
docker container ls -all
```

查看容器的输出，Shell 的输出

```sh
docker container logs [container id]
```

### 创建或运行

以某个 image 为模版，启动容器，会自动检测本地是否存在相应的 image 文件，若不存在，则自动 pull, 每次运行都会创建一个新的容器文件（即不理会之前是否以这个模版启动过容器）

```sh
docker container run [image name]
```

重新启动某个已停止的容器

```sh
docker container start [container id]
```

启动容器 ubuntu 中的 bash

```sh
docker container run -it ubuntu bash
```

运行容器中的某个可执行程序

```sh
docker container exec [options] [container id] [executable]
```

端口映射，本机端口 8000 映射到 Docker 端口 3000

```sh
docker container run -p 8000:3000 -it [image name] /bin/bash
```

启动一次性的容器, `rm` 选项，容器运行结束后自动删除容器文件

```sh
docker container run -rm -p 8000:3000 -it [image name] /bin/bash
```

### 停止

停止容器运行，发送 SIGKILL 信号

```sh
docker container kill [container id]
```

停止容器运行，发送 SIGTERM 信号，如果程序响应这个信号，则可以进行资源清理工作

```sh
docker container stop [container id]
```

### 删除

删除容器文件

```sh
docker container rm [container id]
```

### 其他

将容器中的文件复制到本机中

```sh
docker container cp [container id]:[/path/to/file/in/docker] [path/to/local/machine]
```

## 通过 Dockerfile 文件创建 image 文件

指定 image 文件名称

```sh
docker image build -t [image name] [path/to/dockerfile]
```

指定 image 文件名称即标签名

```sh
docker image build -t [image name]:[tag name] [path/to/dockerfile]
```

## 发布 image

登陆

```sh
docker login
```

为本地 image 标注名称和版本

```sh
docker image tag [image name] [username]/[repository]:[tag]
```

不标注名称，直接构建

```sh
docker image build -t [username]/[repository]:[tag] [path/to/local/image]
```

发布 image

```sh
docker image push [username]/[repository]:[tag]
```

## 编写 Dockerfile 文件

```dockerfile
FROM node:8.4 # 使用官方的 Node image, 标签为 8.4
COPY ./app # 将当前目录下的所有文件（除 .dockerignore 中指明的），复制到 image 文件中的 /app 目录中
WORKDIR /app # 指定工作目录
RUN npm install # 在 /app 下，运行 npm install 安装依赖，运行所有依赖并打包到 image 中
EXPOSE 3000 # 将容器的 3000 端口暴露出来，允许外部连接到这个端口
CMD node app/index.js # 容器启动后自动执行该命令
```

> RUN 与 CMD 的区别
> 
> RUN 在 image 文件的构建阶段执行，执行结果会打包到 image 文件
> CMD 在容器启动后执行
> 
> 一个 Dockfile 可以有多个 RUN 命令，但只能有一个 CMD 命令
> 在 Dockerfile 中指定 CMD 命令，若启动容器过程中 docker container run 手动添加了命令，则以手动输入的这个为准

