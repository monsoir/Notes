# 官方文档阅读摘要

## 多线程编程

[About Threaded Programming](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/AboutThreads/AboutThreads.html#//apple_ref/doc/uid/10000057i-CH6-SW2)

- [多线程中的术语](./threading-terminology.md)
- [Alternatives to Threads](./alternatives-to-threads.md)
- [Threading Packages](./threading-packages.md)

### 线程管理

[Thread Management](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/10000057i-CH15-SW2)

- [线程开销](./thread-cost.md)
- 创建线程
    - [使用 NSThread](./create-using-thread.md)
    - 使用 POSIX Threads, 使用这种方法创建线程，适用于编写跨平台的 App
- [使用 NSObject 来创建线程](./spawn-thread-using-nsobject.md)
- [配置 NSThread 属性](./configure-thread-attributes.md)
- Writing Your Thread Entry Routine
- [结束一个线程](./terminate-a-thread.md)

