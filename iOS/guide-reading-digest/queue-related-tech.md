# Dispatch Queues 涉及到的技术

## Dispatch groups

用于监听一组 block 任务的完成，可以以同步或异步的方式进行监听

## Dispatch semaphores

就是那个「信号量」

dispatch semaphores 与传统的，平时我们说的 semaphores 大致相同，不过效率更高

当信号量不可用时，dispatch semaphores 就会通知内核阻塞某个线程

## Dispatch sources

在接收到系统的某些特定事件时，dispatch source 就会发送通知。用户可以对这些通知进行监听，间接就是对系统的事件进行了监听

用户可以定义一些事件处理程序，当某些系统事件发生时，dispatch source 就可以将这些处理程序提交并异步执行

