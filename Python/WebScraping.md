# Web Scraping

- [配置环境](#配置环境)
	- [安装虚拟环境](#安装虚拟环境)
	- [验证安装](#验证安装)
- [正式开始](#正式开始)
- [概念](#概念)
	- [孩子标签](#孩子标签)
	- [后代标签](#后代标签)
- [常用方法](#常用方法)
	- [.get_text()](#get_text)
	- [find 与 findAll](#find()-与-findall())                                                                                        
	- [.children 与 .descendants](#children-与-descendants)
	- [.next_siblings](#next_siblings)
	- [.parent 和 .parents](#parent-和-parents)
	- [获取属性](#获取属性)
	- [使用 Lambda 表达式过滤](#使用-lambda-表达式过滤)
- [BeautifulSoup 的常见对象](#beautifulsoup-的常见对象)
- [使用 Scrapy 进行采集](#使用-scrapy-进行采集)
	- [安装 Scrapy](#安装-scrapy)
	- [创建 Scrapy](#创建-scrapy)
	- [创建一个爬虫](#创建一个爬虫)
	- [运行爬虫](#运行爬虫)
	- [设置日志处理方式](#设置日志处理方式)
- [Extra](#extra)
- [Reference](#reference)

## 配置环境

### 安装虚拟环境

[跳转到 Django Book 相应部分](./Django%20Book.md#创建-python-虚拟环境)

### 安装 BeautifulSoup 4

进入到虚拟环境，运行命令

```shell
pip install beautifulsoup4
```

### 验证安装

进入到 Python Shell 环境

```py
from bs4 import BeautifulSoup
```

若能成功，则说明已经安装成功

## 正式开始

### 需要引入的文件

```py
from urllib.request import urlopen # 请求库
from bs4 import BeautifulSoup # BeautifulSoup
```

## 概念

### 孩子标签

child

子标签是一个父标签的下一级

### 后代标签

descendant

后代标签是一个父标签下面所有级别的标签

**所有的子标签都是后代标签，但不是所有的后代标签都是子标签**

## 常用方法

### .get_text()

将 HTML 文档中所有的标签清楚，返回一个只包含文本的字符串

通常在准备打印，存储和操作数据时，应在最后才使用 `.get_text()`，一般情况下，应尽可能保留 HTML 标签结构

### find() 与 findAll()

定义：

`findAll(tag, attributes, recursive, text, limit, keywords)`
`find(tag, attributes, recursive, text, keywords)`

find 等价于 findAll 的 limit 等于 1 的情形
recursive 是否递归查找，默认为 `true`， 会标签的所有子标签，以及它们的子标签
text 使用标签的文本内容去匹配（而不是标签属性，如 CSS 属性之类的）
keywords 关键字参数，选择具有指定属性的标签

```py

allText = bsObj.findAll(id="text")
print(allText[0].getText())
```

使用关键字参数时，由于 `class` 是 Python 的关键字，因此需要使用 `class_` 或者 `"class"` 用双引号包围

### .children 与 .descendants

使用 `.children` 处理子标签

`.descendant` 会处理所有后代标签

BeautifulSoup 默认处理当前标签的后代标签

### .next_siblings

处理当前标签的兄弟标签，不包括当前标签

只调用当前标签后面的兄弟标签

例如，作用于 table 时，就可以过滤掉 table 的标题

同理，还有 `.previous_siblings`，`next_sibling` 和 `previous_sibling`

### .parent 和 .parents

查找父标签

### 获取属性

对于一个标签对象，可以通过 `attrs` 获取它的全部属性

```py
aTag.attrs
```

返回一个 Python 字典

如，获取一个 `<img>` 的图片地址

```py
aImgTag.attrs["src"]
```

获取一个 `<a>` 的 `href`

```py
anATag.attrs["href"]
```

### 使用 Lambda 表达式过滤

BeautifulSoup 允许吧特定函数类型当作 `findAll()` 的参数，该函数的限定条件为

- 必须把一个标签作为参数
- 返回结果是 布尔类型

BeautifulSoup 根据这个函数的结果过滤每个标签，若结果为 `true`，则保留标签，否则提出标签

```html
<div class="body" id="content"></div>
<span style="color:red" class="title"></span>
```

过滤：

```py
# 只要属性有两个的标签
soup.findAll(lambda tag: len(tag.attrs) == 2)
```

## BeautifulSoup 的常见对象

### BeautifulSoup 对象

```py
bsObj = BeautifulSoup(html, "html.parser")
```

bsObj 就是一个 BeautifulSoup 对象

### Tag 对象

标签，通过 `find` 和 `findAll` 查找得到一列对象或单个对象

### NavigableString 对象

不常用

用来表示标签中的文字，不是标签

### Comment 对象

不常用

查找 HTML 的注释

## 使用 Scrapy 进行采集

[Scrapy 官网地址](https://scrapy.org)

### 安装 Scrapy

支持 Python 2.7 和 Python 3.3 或以上

```shell
pip install Scrapy
```

### 创建 Scrapy 项目

```shell
scrapy startproject wikiSpider
```

项目目录结构：

- 📃 scrapy.cfg
- 📁 wikiSpider
	- 📃 `__init__.py`
	- 📃 items.py
	- 📃 middlewares.py
	- 📃 pipelines.py
	- 📃 settings.py
	- 📁 spiders

### 创建一个爬虫

在 wikiSpider/wikiSpider/spiders/ 中：

- 添加一个爬虫文件，如 articleSpider.py
- 在 items.py 中定义一个模型类，如 Article

在 Item.py 中，引入依赖

```py
from scrapy import Item, Field
```

定义 model

```py
class Article(Item):
	title = Field()
```

每个 Item 对象表示网站上的一个页面

title 表示页面上的 title 字段，还有其他的字段，如 url, content, header, image 等

---

ArticleSpider 与 wikiSpider 的区别：

ArticleSpider 是 wikiSpider 中一员，仅用于维基词条页面的采集

> 对于信息类型较多的大网站，可能需要为每种信息(如，博客的博文，图书出版发行星系，专栏文章)设置独立的 Scrapy.Item，每个 Item 有不同的字段，但所有 Item 都在同一个 Scrapy 项目中运行

### 运行爬虫

```shell
scrapy crawl article
```

命令使用 Item 名称 article 来调用爬虫

这里的 article 不是类名，不是文件名，而是 **由 articleSpider.py 文件中的 ArticleSpider 类的属性 name="article" 决定**

### 设置日志处理方式

Scrapy 默认把生成的调试信息全部输出到控制台。这可以通过设置日志显示层级来控制

通过在 setting.py 中手动添加配置，setting.py 中没有这个配置，但是在默认配置中配置了

```py
# ....

LOG_LEVEL = 'ERROR'

# ....
```

日志的 5 种层级，后一个层级会把前一个层级的信息显示出来，即 INFO 会把所有的信息显示出来

- CRITICAL
- ERROR
- WARNING
- DEBUG
- INFO

---

同时，可以将日志输出到文件

```shell
scrapy crawl artical -s LOG_FILE=wiki.log
```

若目录中没有 wiki.log 文件，则会新建

若目录中已存在 wiki.log 文件，则会在原文中追加新的日志内容

---

Scrapy 使用 Item 对象决定要从页面中提取什么信息，同时提供不同输出风格来保存信息，如 CSV, JSON, XML

```shell
scrapy crawl article -o articles.csv -t csv
scrapy crawl article -o articles.json -t json
scrapy crawl article -o articles.xml -t xml
```

## 存储数据

在 Python 3.x 中，urllib.request.urlretrieve 可以根据文件的 URL 下载文件

```py
from urllib.request import urlretrieve

urlretrieve(<文件路径>, <存储名称>)
```

### Python 与 MySQL 整合

其中一个与 MySQL 交互的 Python 工具，[PyMySQL](https://github.com/PyMySQL/PyMySQL)

---

安装 PyMySQL

```shell
pip install PyMySQL
```

---

与数据库交互例子

```py
import pymysql

conn = pymysql.connect(host='127.0.0.1', unix_socket='/tmp/mysql.sock', user='root', passwd='password', db='mysql')

cur = conn.cursor()
cur.execute("USE scraping")

cur.execute("SELECT * FROM pages WHERE id=1")
print(cur.fetchone)
cur.close
```

这里用到了 **连接／光标** 模式，这是数据库编程中常用的模式

连接模式的作用：

- 连接数据库
- 发送数据库消息
- 处理回滚操作（查询中断时，回到初始状态，一般使用事务控制手段实现状态回滚）
- 创建新的光标对象

光标的作用

- 一个连接可以有多个光标
- 一个光标跟踪一种状态信息，如跟踪数据库的使用状态
- 若有多个数据库，并需要向多个数据库写内容，这是需要多个光标处理
- 光标会包含最后一次查询执行的结果，使用 `cur.fetchone()` 可以获取查询结果

光标和连接使用完毕后，需要关闭，否则会导致 **连接泄漏**，导致数据库资源浪费

--- 

处理 Unicode 字符串，改变编码

```sql
ALTER DATABASE scraping CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;
ALTER TABLE pages CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
ALTER TABLE pages CHANGE title title VARCHAR(200) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
ALTER TABLE pages CHANGE content content VARCHAR(10000) CHARACTER SET utf8mb4 CO LLATE utf8mb4_unicode_ci;
```

分别改变的内容是：

- 数据库
- 数据表
- 两个字段的默认编码
 
## Extra

urllib2 是 Python 2.x 中的标准库，相当于 Python 3.x 中的 urllib，后者是前者的改名

Python 中的正则表达式规则

![](http://ww3.sinaimg.cn/large/006tNc79gy1ffkr93hx0nj30py0p046v.jpg)

---

ImportError: No module named 'sgmllib'

sgmllib 在 Python 3.x 版本中已经被废弃

```py
from scrapy.contrib.linkextractors.sgml import SgmlLinkExtractor
```

需改为

```py
from scrapy.linkextractors import LinkExtractor
```

## Reference

[Python网络数据采集](http://www.ituring.com.cn/book/1709)

