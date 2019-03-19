# 链接标签

在 HTML 中，除了常用的 `<a>` 标签外，一共有三种超链接标签

- `<link>`
- `<a>`
- `<area>`


## link

- 属于元信息链接标签，标签的内容不是直接面向用户的，而是面向浏览器的
- link 标签能够被搜索引擎或浏览器插件识别
    - 如 RSS 的 link 标签，此时浏览器的 RSS 插件可以根据链接提醒用户页面可以使用 RSS 订阅
- link 标签可以将外部资源链接到文档中，即下载对应的文档到浏览器并处理
    - 如，通过 link 标签引入样式表

### 区分 link 标签类型

是的，link 标签还有不同的类型，通过 `rel` 属性进行区分

```html
<link rel="a-type">
```

通常类型分三大类

- 超链接类
- 外部资源类
- 预处理类

#### 超链接类

##### canonical

- 向搜索引擎提示页面的主 URL, 保留该 URL, 避免重复
- 中文直译是「权威」，也就是最具有代表性的

```html
<link rel="cannonical" href="...">
```

##### alternate

- 提示页面的格式，或对于不同语言或设备设置的版本
- 此类标签面向搜索引擎

```html
<link rel="alternate" href="...">
```

如：当页面提供 RSS 订阅时，可以

```html
<!-- 需要指定 type 的值 -->
<link rel="alternate" type="application/rss+xml"  href="...">
```

##### prev 和 next

- 告知搜索引擎和浏览器页面的上一页或下一页

##### 其他

link 还有其他类型

- `rel=help` 链接到帮助页
- `rel-license` 链接到版权页

#### 外部资源类

- 浏览器会主动下载 link 标签中的外部资源，并根据 `rel` 进行不同的处理
- 包括
    - icon 类型 link
    - 预处理类 link
    - modulepreload 的 link
    - 样式表
    - pingback


##### icon 

- 表示页面的 logo
- 如果没有指定，浏览器会自动根据域名根目录，获取目录下的 favicon.ico 文件
    - 因此，准备好 favicon 并指定路径，有助于提高页面性能
- 可以通过 `sizes` 属性置顶 logo 的尺寸

##### 预处理类

- dns-prefetch 提前对域名做 DNS 查询
- preconnect 提前与服务器建立 tcp 连接
- prefetch 提前获取 href 中 URL 的内容
- preload 提前加载 href 中的 URL
- prerender 提前渲染 href 中的 URL 资源

##### modulepreload

- 预先加载(下载并载入内存中，不执行) JavaScript 模块，使得该模块不用在用到时才加载

```html
<link rel="modulepreload" href="aJSFile.js">
```

##### 样式表

加载样式表

```html
<link rel="stylesheet" href="xxx.css" type="text/css">
```

##### pingback

- 表示页面被引用时，向 pingback 中的 URL 发送一个消息


## a 标签

a 标签也可以拥有 `rel` 属性，部分与 link 标签相同

- alternate
- author
- help
- license
- next
- prev
- search

独有的 `rel`

- tag 网页所属的标签
- bookmark 到上级章节的链接
- 辅助标签，面向浏览器或搜索引擎
    - nofollow 告知搜索引擎不要索引
    - noopener 链接目标网页无法使用 opener 获得原来窗口
    - noreferrer 打开目标网页时，无法使用 referrer 获取当前当前页面 url, 即在目标页面无法获取到原来的 URL
    - opener 打开的网页可以使用 `window.opener` 来访问原理啊页面的 window 对象

## area 标签

- 与 a 标签类似，但它是个区域性的链接
- 与 a 标签支持的 `rel` 完全一样
- 拥有 `shape`, `coords` 属性，指明了热区（即在热区范围内可以打开链接）范围
    - 当 `shape=circle || circ` 时，`coords` 支持三个值：圆心， 半径(x, y, r)
    - 当 `shape=rect || rectangle` 时，`coords` 支持两个值：两个对顶角顶点 x1, y1 或 x2, y2
    - 当 `shape=ploy || polygon` (多边形)时，`coords` 至少需要包含六个值，多边形的各个顶点

**area 必须跟 `<img>` 与 `<map>` 标签一起使用，且 `<area>` 为 `<map>` 的子元素**

