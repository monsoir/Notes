# BeautifulSoup 基本使用方法

## 构建虚拟环境

```sh
# 创建
virtualenv venv --no-site-packages

# 启动
source venv/bin/active
```

## 安装

```sh
pip3 install beautifulsoup4
```

## 引入

```
form bs4 import BeautifulSoup as BSS
```

## 使用

### 创建 BeautifulSoup 对象

- 从网络获取 html 文件
- 从本地读取 html 文件

无论哪种方式，我们都读取到了 html 文件内容了，然后使用这个内容来创建 BeautifulSoup 对象

```py
# 网络获取
html = urlopen('address')
# 读取本地文件
input_file = open('a path')
html = input_file.read()

bsObj = BeautifulSoup(html, 'html.parser')
```

- `html.parser` 是 Python 标准库的一个 HTML 解释器
    - 也可以使用第三方开发的解释器，如 `lxml`
    - 见 [安装解析器](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id9)

### 查找元素

- `findAll(tag, attributes, recursive, text, limit, keywords)`
- `find(tag, attributes, recursive, text, keywords)`

#### `findAll`

- 查找所有指定的 `tag`
- `attributes` 可以指定 css 样式来定位某类型元素
- `recursive` 是否要递归查找，默认为 `True`, 即会查找子元素中的 `tag`
- `text` 匹配标签文本内容
- `limit` 限制查找的数目，即查找前 `limit` 个
- `keywords` 关键词参数，可以指定更特殊的标识

#### `find`

- 与 `findAll` 一样
- 但只会查找一个，即 `limit = 1`

#### 处理兄弟标签

- 返回后面的弟
    - `next_siblings` 返回一组标签
    - `next_sibling` 返回一个标签

- 返回前面的兄
    - `previous_siblings`
    - `previous_sibling`

##### 注意

[官方文档中对 next_sibling 的说明](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#next-sibling-previous-sibling)

> 实际文档中的tag的 .next_sibling 和 .previous_sibling 属性通常是字符串或空白

```html
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a>
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>
```

第一个 `<a />` 的 `next_sibling` 是顿号和换行符，因此，访问下一个 `<a />` 需要 `<a />.next_sibling.next_sibling`


