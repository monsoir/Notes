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

## Run Loops

[Run Loops](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1)

- [Run Loops 总览](./runloops-summary.md)
- [Run Loop 剖析](./anatomy-of-run-loop.md)
    - [Run Loop 模式](./runloop-modes.md)
    - [Input sources](./input-sources.md)
    - [Timer sources](./timer-source.md)
    - [Run Loop observer](./run-loop-observer.md)
    - [Run Loop 的周期性事件](./run-loop-sequence-events.md)
- [什么时候会用到 Run Loop](./when-to-use-a-run-loop.md)

## 并发编程

- [Operation Queues](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html#//apple_ref/doc/uid/TP40008091-CH101-SW1)
    - [基于 Operation 的特点](./operation-advantages.md)
    - [关于 Operation](./about-operation.md)
    - [创建 Operation 对象](./creating-operation.md)
    - [自定义 Operation](./custom-operation.md)
    - [KVO 的属性](./operation-kvo.md)
- [Dispatch Queues](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html#//apple_ref/doc/uid/TP40008091-CH102-SW1)
    - [基于 GCD 的特点](./gcd-advantages.md)

