# Django Book

### 创建项目

- `django-admin.py startproject <project name>`
- 目录结构
	- ![p1.png-w300](http://ww4.sinaimg.cn/large/006tKfTcgw1f6sjpihkfwj30cs07mdgd.jpg)
	- - manage.py
		- 命令行工具，允许以多种方式与 Django 项目进行交互
		- 此文件的生成只是为了方便
	- __init__.py
		- 把该目录当成一个开发包所需的文件
		- 一般不需要修改
	- settings.py
		- Django 项目的设置或配置
		- 查看文件可用的设置类型和默认值
	- urls.py
		- Django 项目的 URL 设置
		- 可视为网站目录
	- wsgi.py
		- 兼容 WSGI 的服务器入口

### 运行项目

- `python manage.py runserver`
- `python manage.py runserver <port>`
	- 修改运行端口
- `python manage.py runserver 0.0.0.0:<port>`
	- 让服务器侦听任意的网络接口

### 视图
- 一个视图就是 Python 的一个函数
- 使 Python 函数成为一个 Django 可识别的视图，必须条件
	- 函数的 1st 参数类型是 HttpRequest
	- 函数返回一个 HttpResponse 实例

### URLconf
- Django 所支撑网站的目录
- 本质是 URL 模式 以及 要为该 URL 模式调用的视图函数之间的映射表
- 即 urls.py 文件

### App vs. Project
- App
	- Is a Web application that does something
- Project
	- Is a collection of configuration and apps for a particular website
- A project can ==contain== multiple apps, an app can ==be in== multiple projects

### Instructions
- `include()`
	- Function
	- Allows referencing other URLconfs
	- Make it easy to plug-and-play URLs (easy to change whatever the URL is)
	- Always use `include()` when include other URL patterns
- `url()`
	- In URLconf, regular expressions are compiled the first time the URLconf module is loaded
		- Means super fast

### Command

- Start project
	- `python manage.py runserver`
- Apply changes to models, and store the changes as a migration
	- `python manage.py makemigrations <app name>`
	- This would generates files for changes for models
- See what SQL that migration would run
	- `python manage.py sqlmigrate <app name> 0001`
- Check for any problems in project without makeing migrations or touching the database
	- `python manage.py check`
- Take all the migrations that haven't been applied, synchronizing hte changes made to models with the schema in the database
	- `python manage.py migrate`
- Invoke Django shell, which sets the DJANGO_SETTINGS_MODULE environment variable
	- `python manage.py shell`
- Create admin user
	- `python manage.py createsuperuser`

### Three-step guide to making model changes
1. Change your models (in `models.py`)
2. Run `python manage.py makemigrations` to create migrations for those changes
3. Run `python manage.py migrate` to apply those changes to the database

### Understanding 

- `r'^(?P<question_id>\d+)/vote/$'`
	- [Click me to understand](https://gist.github.com/bengolder/3f2a86041042fc01c3b0)
- Common idiom to
	1. Load a template
	2. Fill a context
	3. Return an HttpResponse object with the result of the rendered template

```py
	# Shortcut to render the template
	
	# Before using shortcuts
	from django.template import loader
	def index(request):
	    latest_question_list = Question.objects.order_by('-pub_date')[:5]
	    template = loader.get_template('polls/index.html')
	    context = {'latest_question_list': latest_question_list,}
	    return HttpResponse(template.render(context, request))
	    
   # Using Shortcuts
  from django.shortcuts import render
  def index(request):
		latest_question_list = Question.objects.order_by('-pub_date')[:5]
    	context = {'latest_question_list': latest_question_list, }
    	return render(request, 'polls/index.html', context)
```

### Shortcuts

- Introduced in the `django.shortcuts` module
- `render(...)` <=> `HttpResponse(template...)`
- `get_object_or_404(...)` <=> `try...except...`
- `get_list_or_404(...)` <=> `try...except...`

# 其他事

### 寻找 Django 安装路径

```cmd
$ python
>>> import django
>>> django.__file__
```

### 查看 Django 版本

- Method 1

	```cmd
	$ python 
	>>> import django
	>>> django.get_version()
	```

- Method 2
	
	```cmd
	python -m django --version
	```

### 创建 Python 虚拟环境 virtualenv
1. 使用 Python3 安装 
	- `pip3 install virtualenv`
2. 创建项目目录

	```cmd
	$ mkdir <project name>
	$ cd <project name>
	```
3. 创建独立的 Python 运行环境

	```cmd
	$ virtualenv --no-site-packages <virtual environment name>
	# Wait for a few seconds
	
	# Enter the virtual environment
	$ source <virtual environment name>/bin/activate
	
	# Then the prompt turns into
	(<virtual environment name>)...$ 
	# Now the Python version will change, in this example, Python version will be Pyhto3.5
	
	# Exit the virtual environment
	(<virtual environment name>)...$ deactivate
	
	```

### Problem Solved

- `-bash: /Users/Mon/Developer/Python/The Django Book/Playground/mysite/venv/bin/pip: "/Users/Mon/Developer/Python/The: bad interpreter: No such file or directory`
	- Path can not have any blank spaces
- When `pip install Django`, pay attention to the `D` of `Django`, it should be the capital D

