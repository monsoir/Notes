# 使用 Docker 搭建 Redis 服务

使用 Docker 搭建一个可用的 Redis 很简单，DockerHub 上也有官方制造的 image, 直接下载运行就可以了。但难点，或者说是烦点在于

- 如何为这个 Redis 添加密码保护
- 如何为指定 Redis 的数据持久化保存路径
- 如何为从另一个主机/容器里访问这个 Redis 服务

下面就在官方 image 的基础上，记录满足以上需求的过程

## 获取官方 image

```sh
docker pull redis
```

这一步其实也可以不做，因为在下面的步骤中，Docker 的命令会在本地没有镜像的情况下自动获取

## 编辑 Redis 配置文件

对 Redis 的安全设置，如密码等，都是存放在 `/usr/local/etc/redis/redis.conf` 中，那当需要编辑这个文件时，需要进入容器里面去修改吗？

不需要（实际上，在我自己的实践中，好像也进不去）。我们只需要在外面写好配置文件 A，在创建容器时利用 Docker 提供的能力将 A 替换到 image 中的即可

---

记得 Redis 的配置有一大坨，怎么去写好呢？

Redis 官方提供了一个完整的配置文件，我们可以在[官网下载](http://download.redis.io/redis-stable/redis.conf)

```sh
wget -O /local/path/to/redis.conf http://download.redis.io/redis-stable/redis.conf
```

### 配置密码

找到 `requirepass` 的字段，取消注释，写上自己的密码

### 取消 daemonize

记得在非 Docker 环境下，如果先要 Redis 启动后以后台形式运作，那么我们要将 `daemonize` 设置为 `yes`

但经过实验，在 Docker 环境下，我们不能将 `daemonzie` 设置为 `yes`, 一定要设置为 `no`, 否则 Redis 容器启动后会立即退出，没发现是什么原因 😭

那如果我们想要将 Redis 服务在后台运作要怎么办？**直接把 Redis 容器后台运行就好了**

至此，针对目前的需求，对 Redis 配置文件的编辑就完成了

## 编写 Dockerfile

```Dockerfile
FROM redis
COPY ./redis.conf /usr/local/etc/redis/redis.conf
CMD ["redis-server", "/usr/local/etc/redis/redis.conf"]
EXPOSE 6379
```

这些指令的意思从上而下意义是

- 需要的镜像
- 创建镜像时，将前者的内容复制到后者的路径中，即宿主机文件复制到容器中。要注意的是，宿主机文件路径这里，写的应该是**相对路径**
    - 于是，在这里，我们就把自己写好的 Redis 配置文件替换掉镜像自带的配置文件
- 容器启动后执行的命令
    - 由于 Redis 配置文件被我们替换了，于是这里就使用我们的配置文件启动了 Redis 服务
- 将容器的 6379 端口暴露出来，方便我们调用 Redis 服务

## 创建镜像

我们写 Dockerfile 的目的，实际上是为了创建一个自定义的镜像，这个镜像就建立在官方的 Redis 镜像之上，并且定制了配置

```sh
sudo docker image build -t redis-authed .
```

这里需要注意的是最后一个 `.`, 指的是 Dockerfile 的路径

> 所以这里也隐含了我是在 Dockerfile 所在的文件夹中进行镜像的制作


之后，我们可以使用 `sudo docker image ls` 找到刚创建的镜像

## 创建容器

```sh
sudo docker run -v /absulote/path/of/host/to/store:/data --name redis-authed-container -d redis-authed
```

- `-v /absulote/path/of/host/to/store:/data` 指的是将容器中的 `/data` 路径映射到宿主机的 `/absulote/path/of/host/to/store`, 而容器中的 Redis 数据是存放在 `/data` 中。两者都应该**是绝对路径**
- `--name redis-authed-container` 指的是将这个容器命名为 `redis-authed-container`, 方便之后引用
- `-d` 指的是将容器置于后台运行，于是在这里，我们放弃使用 Redis 自带的后台运行，转而使用容器的后台运行方式
- `redis-authed` 指的是在上面步骤中创建的镜像名称

之后，我们可以使用 `sudo docker container ls` 查看我们刚才创建的容器，而且，这个命令展示的是正在运行的容器

> 即，若发现我们创建的容器在结果集中，则说明 Redis 容器正在后台运行了
> 而如果在 Redis 配置文件中将 `daemonize` 设置为 `yes` 时，这个命令中并不会出现我们创建的容器

## 从另一个容器或者机器上访问 Redis 服务

实际上，Docker 的目的就是让我们可以在一个物理机上模拟出两个机器，使得其中的环境可以隔离开来

因此，需要注意：

- 两个容器 A, B, 虽然都是在同一台物理机上，但在运行过程中，我们应该把 A, B 看成是分别部署在两个物理机上。即物理上同机，逻辑上不同机
- 因此，当容器 A, B 想相互通信，或 A 调用 B 的服务时，就需要使用真实的 IP 来定位各自的位置
    - 这里「真实的 IP」是相对于我们常用的 `localhost` 而言
    - 因此，当 A 服务端口为 1000, B 服务端口为 2000, 若 A 想通过 `localhost:2000` 调用 B 服务，是不可行的，因为在逻辑上，A 与 B 不在同一个机器上，`localhost` 是不能相通的

当我们通过 SSH 登陆上服务器，想连接到 Redis 服务查看数据时，需要

```sh
# 获取到容器 redis-authed-container 的 IP
# redis-authed-container 就是我们刚创建的容器名称
sudo docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' redis-authed-container
# ==> 返回结果如：172.17.0.2

# 连接到 Redis 服务
sudo docker run -it --rm redis redis-cli -h 172.17.0.2 -p 6379
# ==> 172.17.0.2:6379>
```

这里注意：

- `-it` 表示进入到交互界面
- `--rm` 表示的是，开启一个用完即弃的容器
- 我们在这里使用的镜像是 redis 而不是 redis-authed, 其实都一样，就少打几个字母

> 使用 Docker 命令的一个规律
> `docker run -it --rm redis` 这一部分，是 Docker 自身的命令
> `redis-cli -h 172.17.0.2 -p 6379` 是 Redis 自身的命令
> 即 `docker's command serve's command` 的形式，就可以使得我们可以直接使用 Docker 执行目标服务的命令了

事实上，同一物理机上的两个容器，还是可以**不通过** IP 来进行通信

```sh
sudo docker run -it --rm --network container:redis-authed-container redis redis-cli

# ==> 127.0.0.1:6379>
```

没错，世界就是这么矛盾，因为 Docker 也还是人造的，必定有办法绕过去的，不过「逻辑上的物理隔离」这个概念是需要清楚的

---

到了这一步，我们在 Redis 交互终端随便输入 `get abc`, 发现

```
(error) NOAUTH Authentication required.
```

那我们在输入密码

```
127.0.0.1:6379> auth <password>
127.0.0.1:6379> get abc
127.0.0.1:6379> OK
127.0.0.1:6379> get abc
127.0.0.1:6379> (nil)
```

☑️ 说明密码保护可以了
☑️ 不同容器访问 Redis 服务也可以了

当我们在 Redis 上存储一点数据后，可以发现，我们之前映射的数据保存路径中，也多了 `dump.rdb` 文件

☑️ 说明指定 Redis 的数据持久化保存路径也可以了


## References

- [获取容器在物理机上的 IP 地址](https://www.notion.so/Kvasir-book-query-proxy-4af87e9861444964b2065eceab807fe0#1331744d320d47dda7cc74b2b0180fe3)
- [同一台物理机上，不同容器之间的关系](https://www.notion.so/Kvasir-book-query-proxy-4af87e9861444964b2065eceab807fe0#eba469e1b412467789e53c1122b0a532)

