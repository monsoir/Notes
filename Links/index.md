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

### Xcode

- [HOW TO HIGHLIGHT YOUR TODOS, FIXMES, & ERRORS IN XCODE](https://krakendev.io/blog/generating-warnings-in-xcode)
    - 在 Xcode 中添加自定义的提示

## Node.js

- [Switching from cluster module to PM2 & RabbitMQ in Node.js](https://medium.com/the-node-js-collection/switching-from-cluster-module-to-pm2-rabbitmq-in-node-js-d0cce5eb96f4)
    - 如何使用 PM2 代替 cluster 模块

## React

- [React 16 — What can it do for you?](https://blog.bitsrc.io/react-16-what-can-it-do-for-you-part-1-e2ee4b9022fb)

## Python

- [Python 3 Module of the Week](https://pymotw.com/3/)
    - 关于如何使用 Python 3 标准库的一系列文章与实例

## 产品

- [ProductHunt](https://www.producthunt.com)
    - 发现新产品

## 原型设计工具

- [Adobe XD](https://www.adobe.com/tw/products/xd.html)
    - 官方免费

## 图片素材

- [GRATISOGRAPHY](https://gratisography.com)
    - 免费，可商用图片网站

