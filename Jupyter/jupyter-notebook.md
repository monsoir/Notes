# Jupyter Notebook

[Jupyter](http://jupyter.org) Notebook 是使用 Python 开发的一款浏览器编辑器，可以用来实时运行代码，记录笔记

Jupyter Notebook 由于是使用 Python 开发的，因此能安装上 Jupyter Notebook 就肯定有 Python 的环境，因此 Jupyter Notebook 默认支持使用 Python

而对于其他语言，则需要另外安装对应的内核

## 安装与启动

在 Mac 上，可以使用 pip 来进行安装，前提是已经安装了 Python 3, 也建议使用 Python 3

```sh
# 我的电脑上 Python 2 和 Python 3 共存了，所以使用 pip3 来指明使用 Python 3 的 pip
pip3 install jupyter
```

---

选定一个目录，作为你的笔记存放的地方，并在该目录下运行命令

```sh
jupyter notebook
```

此时，将会自动在浏览器上打开 [http://localhost:8888](http://localhost:8888)

--- 

若需要关闭，则在原来启动 Jupyter Notebook 的命令行下键入 `ctrl + c`

