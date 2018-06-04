# 链接

## iOS

### Objective-C

- [Enumerated Types and Checking Their Values](http://sam.dods.co/blog/2014/02/01/checking-for-a-value-in-a-bit-mask/)
    - 如何使用 `NSOPTION` 进行选项的组合与判断

### 队列，多线程

- [Introduction to iOS Concurrency](https://medium.com/shakuro/introduction-to-ios-concurrency-a5db1cf18fa6)
    - 关于队列，同步与异步，并行的图文说明
- [Dispatch Queues](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html)
    - 官方文档对于队列的说明
- [GCD's Main Queue vs. Main Thread](http://blog.benjamin-encz.de/post/main-queue-vs-main-thread/)
    - 阐述了：确保要在主线程中工作的任务，不能简单地判断当前线程是否为主线程，还需要判断当前队列为主队列
    - 主要原因，是当在队列 A 中同步执行其他队列 B 中的任务时，任务当前的队列仍然为 A, 而不是 B, 所以，如果当 `B == 主队列` 的时候就出问题了
- [iOS中的主线程（Main Thread）与主队列（Main Queue）](http://blog.corneliamu.com/archives/74)
    - 对上一篇文章的一个中文补充

### 离屏渲染

- [iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)
- [圆角卡顿刨根问底](http://awhisper.github.io/2016/03/12/滚动圆角卡顿刨根问底/)

### Xcode

- [HOW TO HIGHLIGHT YOUR TODOS, FIXMES, & ERRORS IN XCODE](https://krakendev.io/blog/generating-warnings-in-xcode)
    - 在 Xcode 中添加自定义的提示

## Node.js

- [Switching from cluster module to PM2 & RabbitMQ in Node.js](https://medium.com/the-node-js-collection/switching-from-cluster-module-to-pm2-rabbitmq-in-node-js-d0cce5eb96f4)
    - 如何使用 PM2 代替 cluster 模块
- [How to Watch for Files Changes in Node.js](http://thisdavej.com/how-to-watch-for-files-changes-in-node-js/)
    - 使用 Node.js 检测文件变化

## React

- [React 16 — What can it do for you?](https://blog.bitsrc.io/react-16-what-can-it-do-for-you-part-1-e2ee4b9022fb)

## Python

- [Python 3 Module of the Week](https://pymotw.com/3/)
    - 关于如何使用 Python 3 标准库的一系列文章与实例

## Redis

- [Install and config Redis on Mac OS X via Homebrew](https://medium.com/@petehouston/install-and-config-redis-on-mac-os-x-via-homebrew-eb8df9a4f298)
    - Redis 简单的安装使用方法
    - 守护进程启动 `brew services start redis`
    - 阻塞进程启动 `redis-server /usr/local/etc/redis.conf`

## 产品

- [ProductHunt](https://www.producthunt.com)
    - 发现新产品

## 原型设计工具

- [Adobe XD](https://www.adobe.com/tw/products/xd.html)
    - 官方免费
- [Launchaco](https://www.launchaco.com)
    - 在线制作网页，Logo 原型

## 图片素材

- [GRATISOGRAPHY](https://gratisography.com)
    - 免费，可商用图片网站
- [Cool Backgrounds](https://coolbackgrounds.io)
    - 纯颜色背景图片

## 在线工具

### iOS

- [iOS Libraries](http://ioslibraries.co)
    - iOS 第三方库搜索
- [AppSight](https://www.appsight.io)
    - 查看热门 App 使用了哪些第三方库
- [Transformer](https://andresinaka.github.io/Transformer/)
    - `NSAttributedString` 可视化编辑，代码输出
- [iOS Ref](https://iosref.com)
    - 关于 iOS 设备，开发的一些文档查询
- [iOSRes](http://iosres.com)
    - iOS 设备屏幕分辨率查询
- [Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/)
    - 官方设计规范
    - 包括了图标大小的设定，不过需要找一下

### JavaScript

- [Regulex](https://jex.im/regulex)
    - JavaScript 正则表达式验证

### CSS

- [CSS GRID 12 COLUMN LAYOUT](https://erikmonjas.github.io/css-grid-12-column-layout/)
    - CSS Grid 布局演示及其代码

### 其他

- [Carbon](https://carbon.now.sh)
    - 代码片段图片生成器
- [Time.is](https://time.is)
    - 不同时区的时间
- [asciiflow](http://asciiflow.com)
    - 使用 ASCII 码画流程图
- [Code Beautify](https://codebeautify.org)
    - 代码格式美化
- [wordmark.it](https://wordmark.it)
    - 输入一个单词，用本机上所含有的字体渲染出来
- [learn Anything](https://learn-anything.xyz)
    - 学习一门技术的系统概览
- [myjson](http://myjson.com/api)
    - JSON 存储托管
- [SMS Receive Free](https://smsreceivefree.com)
    - 免费 SMS 信息接收
- [DevDocs API Documentation](https://devdocs.io)
    - API 文档查询

