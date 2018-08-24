# How Browsers Work

[How Browsers Work](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)

## 浏览器结构

- The user interface 用户界面
- The browser engine 浏览器引擎
    - 协调 UI 操作与渲染引擎
- The rendering engine 渲染引擎
    - 负责渲染请求内容，转换 HTML, CSS
- Networking 网络
    - 处理诸如 HTTP 的请求，各个浏览器根据平台独立的接口开发开发了各自的实现
- UI backend
    - 绘制组件和窗口，暴露了通用的接口，每个浏览器根据不同的操作系统开发各自的实现
- JavaScript interpreter JavaScript 解释器
    - 解释和执行 JavaScript 代码
- Data Storeage 数据存储
    - 持久化存储层，存储如 cookies 等数据
    - 存储机制有 localStorage, IndexedDB, WebSQL, FileSystem

![Browswer components](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/layers.png)

> 在 Chrome 中，运行着多个渲染引擎的实例，每个 tab 对应一个独立进程

## 渲染引擎

用于绘制现实请求的内容，默认情况下，渲染引擎可以显示 HTML, XML 和图片

而通过插件的形式，可以实现显示 PDF 等其他文件

各个浏览器使用的渲染引擎

- IE: Trident
- Firefox: Gecko
- Safari: WebKit
- Chrome, Opera(from v15): Blink(fork of WebKit)

> WebKit 是 Linux 上开源的渲染引擎，由 Apple 进行改造来支持 Mac 和 Windows

### 渲染工作流

![Rendering engine basic flow](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/flow.png)

#### 构建过程

- 渲染引擎将 HTML 文档中的 DOM 节点转换成 **content tree**, 将 style 数据（包括外部 CSS 和自带的样式），转换后与 **content tree** 结合，创建 **render tree**
- **render tree** 包括了各个矩形元素的可视化属性，如颜色，大小

#### Layout 过程

- 计算每一个节点的位置

#### 绘制过程

- 遍历 **render tree**, 使用 UI backend 来绘制每一个节点

> 以上过程是一个渐进的过程。为了更好的用户体验，渲染引擎会尽可能快地渲染出内容，因此并不会等待整个 HTML 转换完成后才渲染。
> 因此，当解释 HTML 的过程还在进行的时候，一部分的内容就会呈现出来

WebKit 引擎渲染过程

![WebKit main flow](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/webkitflow.png)

Gecko 引擎渲染过程

![Gecko main flow](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/image008.jpg)


## 处理脚本与样式的顺序

### 脚本

网页的模型是同步的，因此当遇到 `<script>` 标签时，浏览器是会以同步的方式处理脚本，当请求服务端的脚本时，则会发生阻塞。

上述特点在 HTML4, 5 的规范中进行了说明，也可以使用 `defer` 属性来标记延迟请求。

HTML5 也添加了选项来标记脚本在其他线程中转换和执行脚本

### 样式

由于样式并不会影响到 DOM 树的结构，所以对于远程的样式，浏览器并不会等待或停止 DOM 树的转换

## Render tree 的构建

当 DOM 树被构建的同时，浏览器还会创建 **render tree**

**render tree** 是真正用于可视化绘制的树

### Render tree 与 DOM tree 的关系

- 非可视化的元素不会插入到 **render tree**
    - 如 `<header>` 标签
    - `display: none` 的元素
    - 但，`visiable: hidden` 会插入到 **render tree**

### 样式应用顺序

由低到高的顺序

1. 浏览器预定义
2. 用户普通定义
3. 网页普通定义
4. 网页 important 定义
5. 用户 important 定义


