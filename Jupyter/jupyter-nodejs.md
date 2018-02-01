# 在 Jupyter Notebook 中使用 Node.js

由于 Jupyter Notebook 默认只支持 Python, 因此，若想在 Jupyter Notebook 中使用其他语言，则需要自行添加支持，而这里的额外支持，就是自行安装 Jupyter 的 Node.js 内核

相关支持👉 [jupyter-nodejs](https://github.com/notablemind/jupyter-nodejs)

## 大概描述一下安装

安装所需要的依赖

- IPython 3.x
- node
- ZeroMQ
- pkg-config

既然需要在 Jupyter 中添加 Node 的支持，那么 IPython 3.x 和 Node 就不需要再多介绍了

---

ZeroMQ

一种基于消息队列的多线程网络库，官网地址👉 [http://zeromq.org](http://zeromq.org)

在 Mac 上使用 Homebrew 安装

```sh
brew install zmq
```

---

pkg-config

```sh
brew install pkg-config
```

---

安装所要需要的东西后，执行以下命令

```sh
# 选取一个目录来存放 jupyter-nodejs 的内容
git clone https://github.com/notablemind/jupyter-nodejs.git
cd jupyter-nodejs
mkdir -p ~/.ipython/kernels/nodejs/
npm install && node install.js
npm run build
npm run build-ext
jupyter console --kernel nodejs
```

安装完成后，jupyter-nodejs 的内容不能删除，不然启动就不成功了

---

安装完成后，jupyter-nodejs 内核默认不支持 ES6 的语法，需要在代码的最上端添加

`%%babel` 才能使用 ES6

