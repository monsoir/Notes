# Web Scraping

- [配置环境](#配置环境)
	- [安装虚拟环境](#安装虚拟环境)
	- [验证安装](#验证安装)
- [正式开始](#正式开始)
- [常用方法](#常用方法)
	- [.get_text()](#get_text)                                                                                        
- [Extra](#extra)

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
 
## Extra

urllib2 是 Python 2.x 中的标准库，相当于 Python 3.x 中的 urllib，后者是前者的改名


