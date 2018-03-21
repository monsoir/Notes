# Node App 部署

## 环境

阿里云，Ubuntu 16.04 64bit

## 安装 Node

最新 Node 版本为 9.x

```sh
curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -
sudo apt-get install -y nodejs
```

```sh
node -v # v9.7.1
npm -v # 5.6.0
```

- [在 Ubuntu 上安装最新版 Node.js](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

## 安装 Express 生成器

- （可选，主要做个 Demo）

```sh
npm i express-generator -g
```

## 创建一个新用户

- 避免使用 root 用户

```sh
sudo adduser alfred
# pswd: alfred1234
```

## 生成一个应用

```sh
express demo
```

## 添加安全组规则

- 需要在服务器实例中，找到安全组规则设置，添加安全组规则
- 由于默认的设置并没有允许 Express 的默认端口的访问，所以，在设置前，我们并不能访问到 `3000` 端口的 Express App

## 杀掉进程

使用本地终端连接阿里云时，开启了 Express 后长时间没操作时，连接断掉，而此时 Express 还在后台运行。重新登录开启 Express 时，会因为之前的 Express App 占用 3000 端口而开启失败，此时，需要先将之前的 Express App 进程杀掉

```sh
ps -aux | grep node
# 或者
ps -ef | grep node

# 找出进程 ID
kill -s 9 <进程 ID>
```

## 使用 pm2 进行进程管理

[pm2](http://pm2.keymetrics.io) 是用于生产环境下的进程管理器，可以在后台运行 Node.js 的 App, 并通过 pm2 来管理 Node.js 的 App

### 安装

```sh
npm i -g pm2
```

### 使用

- 平常我们都是通过 `npm start` 的方式来启动 Express App, 而 `npm start` 本质上是通过运行命令 `node ./bin/www` 来启动
- 使用 pm2 时，方法类似，通过 `pm2 start /bin/www` 来启动





