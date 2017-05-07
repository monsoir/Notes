# Django Book

- [配置环境](#配置环境)
	- [创建 Python 虚拟环境](#创建-python-虚拟环境)
	- [安装 Django](#安装-django)
	- [验证安装](#验证安装)
- [正式开始](#正式开始)
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

## 正式开始

查看 Django 版本

进入到已安装 Django 的 Python 的虚拟环境

```shell
python -m django --version
```

### 创建项目

```shell
django-admin startproject mysite
```

### 开启开发环境服务

进入到 manage.py 文件所在的文件夹，运行命令

```shell
# 默认使用 8000 端口
python manage.py runserver

# 指定端口
python manage.py runserver 8080
```

### 创建应用

```shell
python manage.py startapp polls
```

#### Projects vs. apps

App 是一个网络应用，真正干活的东西，如一个日志系统。
Project 是一个对一个网站，包含配置文件与一个或多个 app 的集合。
一个 project 可以包含多个 app，一个 app 可以在多个 project 中存在。

### 编写 Views，配置路由

#### Views

每一个 View 就是一个函数，每一个访问路由就是调用对应的 View

当调用 View 的时候，将会传入一个请求对象，返回一个 HttpResponse 对象，因此，需要引入

```py
from django.http import HttpResponse
```

#### 路由

##### app 内路由

首先编写 app 内路由，在对应 app 文件内新建一个 urls.py

```py
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

##### 项目路由

app 内路由编写完成后，需要将 app 路由添加到项目路由中，在项目文件 urls.py

```py
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
]
```

#### 重点的是

- 编写 View 的时候，引入 `from django.http import HttpResponse`
- 编写路由的时候，引入 `from django.conf.urls import url`
- 项目路由接入 app 路由时，使用 `include`
- 路由使用 正则表达式 进行匹配

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

### 开启开发环境服务

```
DisallowedHost at /
Invalid HTTP_HOST header: '192.168.1.XXX:8000'. You may need to add '192.168.1.XXX' to ALLOWED_HOSTS.
```

在项目的 settings.py 文件中，在 `ALLOWED_HOSTS` 中添加值

```py
ALLOWED_HOSTS = ['192.168.1.XXX']
```

[Reference](http://stackoverflow.com/a/40582485/5211544)


