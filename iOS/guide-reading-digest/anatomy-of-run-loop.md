# Run Loop 剖析

## Run Loop 与线程的关系

Run Loop 简单来说就是一个循环，一个不断循环的循环。而线程，就是需要依赖这种循环来处理各种事件，执行各种应对事件的处理方法

## Run Loop 处理的事件的来源

一个 Run Loop 大致上会从两个源头获取到事件

- Input sources
- Timer sources

Input sources 传递的是异步事件，如其他线程发送过来的事件，或其他程序发送过来的事件

Timer sources 传递的是同步事件，这类事件一般是在特定的事件发生，或在特定的时间间隔重复发生

## Run Loop 运行过程

![Structure of a run loop and its sources](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/Art/runloop.jpg)

上图是 Run Loop 运行的大致过程，可以看到，有两种 sources 会触发箭头指向 Thread, 说明这两种 sources 的事件会涉及到部分线程的运行状态

对于 Input sources, 即异步事件的到达，会将事件传递到对应的处理方法，并会触发当前线程相关联的 Run Loop 退出，通过调用 `runUntilDate:` 方法

对于 Timer sources, 也会将事件传递到对应的处理方法，但不会导致当前 Run Loop 的退出

## 监听 Run Loop

对于 Input sources, Run Loops 还会发送消息来报告自身的行为。可以通过监听 Run Loops 来接收这些信息



