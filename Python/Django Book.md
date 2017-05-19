# Django Book

- [配置环境](#配置环境)
	- [创建 Python 虚拟环境](#创建-python-虚拟环境)
	- [安装 Django](#安装-django)
	- [验证安装](#验证安装)
- [查看 Django 版本](#查看-django-版本)
- [创建项目](#创建项目)
- [开启开发环境服务](#开启开发环境服务)
- [创建应用](#创建应用)
- [配置路由](#配置路由)
- [配置数据库](#配置数据库)
- [设置时区](#设置时区)
- [INSTALL_APPS](#install_apps)
- [Model](#model)
- [使用 shell 来操作 model 与数据库交互的 API](#使用-shell-来操作-model-与数据库交互的-api)
- [后台管理](#后台管理)
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

## 查看 Django 版本

进入到已安装 Django 的 Python 的虚拟环境

```shell
python -m django --version
```

## 创建项目

```shell
django-admin startproject mysite
```

## 开启开发环境服务

进入到 manage.py 文件所在的文件夹，运行命令

```shell
# 默认使用 8000 端口
python manage.py runserver

# 指定端口
python manage.py runserver 8080
```

## 创建应用

```shell
python manage.py startapp polls
```

### Projects vs. apps

App 是一个网络应用，真正干活的东西，如一个日志系统。
Project 是一个对一个网站，包含配置文件与一个或多个 app 的集合。
一个 project 可以包含多个 app，一个 app 可以在多个 project 中存在。

## 配置路由

### Views

每一个 View 就是一个函数，每一个访问路由就是调用对应的 View

当调用 View 的时候，将会传入一个请求对象，返回一个 HttpResponse 对象，因此，需要引入

```py
from django.http import HttpResponse
```

### 路由

#### app 内路由

首先编写 app 内路由，在对应 app 文件内新建一个 urls.py

```py
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

#### 项目路由

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

## 配置数据库

Django 数据库默认使用 SQLite

使用自定义数据库，需要修改项目下的 settings.py 中 DATABASES 部分的 ENGINE 和 NAME 字段

- ENGINE, 一般取值为以下的值
	- django.db.backends.sqlite3
	- django.db.backends.postgresql
	- django.db.backends.mysql
	- django.db.backends.oracle
	- 当然，还有其他
- NAME
	- 数据库名字
	- 若使用 SQLite，则值为数据库文件的绝对路径
	- 若为其他 DBMS，则需要填写数据库的用户，密码及主机位置

## 设置时区

设置时区为身处的时区，如：中国是 `Asia/Shanghai`

时区参考：[Reference](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

## INSTALL_APPS

INSTALL_APPS 中的 APPS 是一个 Django 实例中被激活使用的 APP

APP 是可以使用在多个 project 中的

默认的 INSTALL_APPS 有：

- django.contrib.admin 管理后台 admin 页面
- django.contrib.auth 用户，权限验证系统
- django.contrib.contenttypes 负责追踪 Django 项目中的 models，提供高层次，范型的接口来操作 models
- django.contrib.sessions session 管理
- django.contrib.messages 消息提示（一次性的那种）
- django.contrib.staticfiles 静态文件管理，用于在生产环境中集中各个 app 中的静态资源到一个单独的位置

默认的 INSTALL_APPS 可以根据实际需要进行删减

INSTALL_APPS 中部分 APP 可能需要到数据库来存储数据，因此在使用它们前，需要创建表，运行命令

```shell
python manage.py migrate
```

上述命令会根据 INSTALL_APPS 中的配置来操作表

## Model

每一个 model 都继承于 `django.db.models.Model`，因此，model 文件中需要引入文件

```py
from django.db import models
```

model 中的每一个字段(field) 都由一个 `Field` 的实例表示（具体是 Field 的子类实例）

### 激活 model

通过定义 model，django 会帮你做了：

- 在数据库中，为 app 创建一个 schema，通过 CREATE TABLE 语句
- 创建访问数据库的 API，来操作 model 对象

但这一切，需要激活 model 才能实现，激活步骤：

- 在项目的配置文件 settings.py 中的 INSTALL_APPS 中添加 app
- 运行命令

配置文件添加：

如在事例应用 polls 中，PollsConfig 类位于 poll(app 文件夹下)/apps.py 中，所以配置值(dotted path) 为 `polls.apps.PollsConfig`，因此，在项目的 settings.py 的 INSTALL_APPS 中修改为：

```py
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

运行命令：

```shell
python manage.py makemigrations polls
```

通过上述命令，告诉 Django，开发者已经对 model 作出了修改并打算将修改存储为 migration

> Migration 是 Django 存储 model 变化，并将变化应用到数据库 schema 的方式，存在方式为普通文件，命名方式形如 `polls/migrations/0001_initial.py`

可以使用命令查看 migrations 文件，它们其实都是 SQL 语句

```shell
python manage.py sqlmigrate polls(app 名称) 0001(migration 文件名称)
```

### Django 建立数据库表的特点

- 表名是由 app 名称与 model 的小写组成（这个可以重写）
- 主键是自动生成（这个可以重写）
- 外键关系由 `FOREIGN KEY` 显式声明

可以使用以下命令来检查数据库的修改问题，此操作并不会影响到数据库中的数据或结构

```shell
python manage.py check
```

最后运行命令

```shell
python manage.py migrate
```

上述命令，使所有还没有生效的 migrations 生效并同步到数据库中

### 总结添加／修改 model 的步骤

1. 添加／修改 model 类
2. 运行命令，产生 migrations 来记录更改

	```shell
	python manage.py makemigrations
	```

3. 运行命令，将更改(migrations)应用并同步到数据库中

	```shell
	python manage.py migrate
	```

> 可能产生疑问的地方，为什么中间需要产生 migrations 文件来？

> 便于开发者将每次对数据库的修改(migration 文件)与 app 一起提交到版本控制中，这样，便于其他开发人员或自己在以后的开发过程中开发，可能需要回顾一下吧


## 使用 shell 来操作 model 与数据库交互的 API

调用 Python shell

```shell
python manage.py shell
```

使用 manage.py 带出 shell 是因为这会帮我们自动将 DJANGO_SETTING_MODULE 环境变量，帮我们引入 settings.py

当然也可以手动引入，可以翻文档看

---

以下操作在 Python shell 中进行

引入需要用到的 models

```py
from polls.models import Question, Choice
```

访问数据库中所有的 Question 数据

```py
Question.objects.all()
```

创建 Question 数据

```py
# 在 Django 中，使用 timezone.now() 代替 Python 标准库中的 datetime.now()
from django.utils import timezone
q = Question(question_text="What's new?", pub_date=timezone.now())

# 显式保存
q.save()
```

Django 提供了丰富的 API 用于过滤数据，如：

```py
Question.objects.filter(id=1)
Question.objects.filter(question_text__startswith='What')
```

查找数据

```py
Question.objects.get(pub_date__year=current_year) # current_year 是定义过的变量，定义步骤忽略
Question.objects.get(pk=1) # 直接通过主键查找
```

直接调用我们在类中自定义的方法

```py
q = Question.objects.get(pk=1)
q.was_published_recently()
```

查找关联到 Question 中的 Choice 集合

```py
q.choice_set.all()
```

通过 Question 中的 Choice set 创建 Choice

```py
c = q.choice_set.create(choice_text='Not much', votes=0) # 自动将 choice 关联到当前的 question
c.question # 可查询到 choice 关联的 question
```

使用两个下划线来表示访问属性，而不是访问 relationship

```py
Choice.objects.filter(question__pub_date__year=current_year)
```

修改数据

```py
# 在上面的 q 基础上进行，直接赋值后保存
q.question_text="What's up?"
q.save()
```

删除数据

```py
c.delete()
```

可以通过重写 `__str__` 方法类自定义更好的类描述输出，如 Question

```py
def __str__(self):
    return self.question_text
```

## 后台管理

创建管理员用户

```shell
python manage.py createsuperuser

# 之后会要求输入一系列信息，如用户名
```

---

Django 的管理员页面是默认开启的，只需要开启服务就行了，而且 Django 的语言国际化是默认开启的

```shell
python manage.py runserver
```

进入到管理员页面 [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/)

可以看见若干个可以修改的内容，它们是由 django.contrib.auth 提供的

---

但是管理员页面并没有看到我们自己新建的 app(poll)，这是由于我们还没有将 polls 中的 model 注册到管理后台

注册管理后台，在 polls/admin.py 中，将需要在后台修改的 model 注册

```py
from django.contrib import admin
from .models import Question

# 注册 model
admin.site.register(Question)
```

## 编写 Views

每一个网页或其他内容，都是通过 views 来承载的，每一个 view 由一个 Python 函数／方法（如果是基于类的 view）呈现

Django 使用 URLconfs 来访问到相应的 view，一个 URLconf 映射 views 的 URL patterns (即通常的正则表达式)

关于使用 URLconfs 的更多信息，参考 django.urls

### View 的定位

当访问一个路径时，如 /polls/34/

1. Django 会加载 mysite.urls 模块（项目的路由文件），这是由于配置文件中的 ROOT_URLCONF 中指定的

	```py
	ROOT_URLCONF = 'mysite.urls'
	```

2. 加载 mysite.urls 模块后，会找到变量 `urlpatterns` 并按顺序遍历其中的正则表达式
3. 当匹配到 `'^polls/'` 后，Django 会去掉路径中的 `'polls/'`，并将剩下的 `'34/'` 发送到 polls.urls 中，并执行与上述步骤类似的操作
4. 当匹配到正则表达式 `r'^(?P<question_id>[0-9]+)/$'` 后，便会调用对应的函数／方法，这里是 views.detail()，并将参数传送过去

	```py
	detail(request=<HttpRequest object>, question_id='34')
	```

	使用圆括号包裹正则表达式可以捕捉到匹配的字符串并传送到对应的视图函数，如 `question_id='34'` 就是来自 `(?P<question_id>[0-9]+)`。同时，?P<question_id> 定义了参数的名字

由于使用了正则表达式来匹配路由，并不需要在 URL 中添加 `.html`

### Views 的工作

View 的职责有两个，最后都需要做到以下其中一件事，其他部分随意

- 返回一个 HttpResponse 对象，其中包含了请求页面的响应内容
- 抛出异常，如 `Http404`

---

View 可以做的事情很多，例如：

- 从数据库中读取信息
- 使用 Django 或第三方的模版系统
- 生成 PDF 文件，输出 XML，创建 ZIP 文件
- ...

#### 模版

模版定义了页面的外观

项目的配置文件中，TEMPLATES 定义了 Django 将如何加载并渲染模版

默认的配置，BACKEND 默认了 DjangoTemplates 模版引擎，APP_DIRS 设置了 True，即 DjangoTemplates 将会在 INSTALLED_APPS 配置了的 App 的文件夹下寻找 templates 文件夹


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


