# Django Book

- [配置环境](#配置环境)
	- [创建 Python 虚拟环境](#创建-python-虚拟环境)
	- [安装 Django](#安装-django)
	- [验证安装](#验证安装)
- [问题解决](#问题解决)
- [遗留笔记](#遗留笔记)

## 配置环境

### 创建 Python 虚拟环境

1. 使用 Python3 安装

	```shell
	pip3 install virtualenv
	```

2. 创建项目目录

	```shell
	mkdir <project name>
	cd <project name>
	```
	
3. 创建独立的 Python 运行环境

	```shell
	virtualenv --no-site-packages <虚拟环境名称>
	# 等待一段时间...
	
	# 进入虚拟环境
	source <虚拟环境名称>/bin/active
	# 此时，命令行头会显示当前虚拟环境的名称

	# 退出虚拟环境
	deactivate
	```
### 安装 Django

在虚拟环境下，执行命令

```shell
pip install Django
# 等待一段时间...
# 安装过程中可能出现多次的超时错误，必要时需要通过代理下载
```

### 验证安装

进入 Python Shell 环境，执行代码

```py
import django
print(django.get_version())
```

## 问题解决

### 路径问题

```
-bash: /Users/XXX/Developer/Python/The Django Book/Playground/mysite/venv/bin/pip: "/Users/XXX/Developer/Python/The: bad interpreter: No such file or directory
```

路径中不能含有空格

### 安装问题

当安装 Django 时，

```shell
pip install Django
```

`Django` 必须的 `D` 为大写

