# 简单使用 Nginx

- Nginx 用于多极集群的负载均衡
- pm2 用于单机的负载均衡

## Ubuntu 下安装 Nginx

```sh
apt-get install nginx
```

安装完成后，访问 `IP:80` 可以看到 `Welcome to nginx`

## 配置文件

- Nginx 的配置文件目录位于 `/etc/nginx/` 下
- 其中，有两个文件夹我们需要注意
    - `sites-enabled` 改目录下的配置文件对应的网站才能被用户访问到
    - `sites-avaliable` 一般在这里编写站点的配置文件，在将配置文件链接到 `sites-enabled`


```sh
ln -s /etc/nginx/sites-avaliable/aSite /etc/nginx/sites-enabled/aSite
```

### 配置文件格式

最顶层

```
server {

}
```

声明监听端口

```
server {
    listen 80;
}
```

- 声明监听的端口，Web 一般都是 80 端口

URL 的匹配

```
server {
    listen 80;
    server_name example.com;
}
```

- `server_name` 用来匹配 URL 地址
- 当请求通过 DNS 解析后，到达 Nginx 时，会查看 URL 并寻找匹配的 `server_name`, 并在服务器内部重定向到对应的服务
- 这使得服务器可以托管多个不同域名的网站

静态文件的配置

```
server {
    listen 80;
    server_name example.com;
    root /var/www/exmaple;
}
```

- `root` 托管静态文件的关键
- 定义静态文件的存放路径

配置 Location

```
server {
    listen 80;
    server_name example.com;
    root /var/www/exmaple;
    
    location / { # 正则表达式匹配
        try_files $uri $uri/ index.html;
    }
}
```

或

```
server {
    listen 80;
    server_name example.com;
    root /var/www/exmaple;
    
    location = / { # 字符串完全匹配
    
    }
}
```

- `location` 接受两个参数：(字符串/正则表达式, 区块)
    - 字符串/正则表达式：用于匹配某个特定路径，如当用户访问 `example.com/whatever` 时，这个参数，即 uri 将会是 `whatever`
    - 区块中的 `try_files` 指令将会不断搜索其后的资源名，并返回第一个存在的资源

带上上述的配置，一个请求的流程大概是

1. 请求来访问 `https://example.com`, Nginx 处理
2. Nginx 找到某个 server 的 `server_name` 是 `example.com`, 并交给这个 server 来处理请求
3. 同时，Nginx 匹配到路径中的 `/`, 并传到变量 `$uri`
4. 在 `location` 的区块中，Nginx 不断搜寻 `try_files` 后的文件或文件夹，当找到第一个存在的资源时，返回

## References

- [写给Web开发人员看的Nginx介绍](https://fraserxu.me/2013/06/22/Nginx-for-developers/)
- [Nginx for Developers: An Introduction](http://carrot.is/coding/nginx_introduction)


