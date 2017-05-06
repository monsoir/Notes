# Web Scraping

- [配置环境](#配置环境)
	- [安装虚拟环境](#安装虚拟环境)
	- [验证安装](#验证安装)
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

## Extra

urllib2 是 Python 2.x 中的标准库，相当于 Python 3.x 中的 urllib，后者是前者的改名


