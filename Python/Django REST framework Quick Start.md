# Django REST framework Quick Start

创建一个简单的 API 来允许 admin 用户查看和修改系统中的用户和用户组

## 搭建环境

创建项目文件夹

```shell
mkdir tutorial
cd tutorial
```

---

创建并进入到虚拟环境

```shell
virtualenv --no-site-packages venv
source venv/bin/activate
```

---

安装依赖

```shell
pip install django
pip install djangorestframework
```

---

创建项目

```shell
# 在虚拟环境文件夹所在的目录下
django-admin.py startproject tutorial . # 注意末尾的 . 符号
cd tutorial
```

---

创建 App

```
django-admin.py startapp quickstart
cd ..
```

首先同步数据库

```shell
python manage.py migrate
```

创建一个管理员账号，项目中会用到

```shell
python manage.py createsuperuser
```


