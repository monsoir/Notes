# Alternatives to Threads

对比线程的其他选择

实际上，这里列出的其他选择，实际上的原理还是基于线程，只是系统将线程封装起来，开发者不需要直接参与到线程的管理工作

## Operation objects

实际上是平常使用的 `NSOperation`

这是对一个 task 的封装，同时，这个 task 一般是在另一个次要的线程中执行

使用时，专注点放在 task 本身，与 operation queue 一起使用

## Grand Central Dispatch(GCD)

使用时，专注点放在 task 本身，GCD 同时会根据设备的可用核心数，将 task 放入对应的队列中，提高执行效率

## Idle-time notifications

适用于任务较短，优先级较低的 task

idle-time notifications 会在应用空闲的时候去执行 task, 即 run loop 空闲的时候

## Asynchronous functions

执行这些 task 时，可能是使用了系统的守护进程或创建了额外的线程来执行

使用时，不需要关心

## Timers

适用于执行周期性的 task, 但这部分 task 又不太需要另外开启线程执行

## Separate processes

开启另外一个独立的进程

一般情况下，一个例子就是利用服务端处理，而不是客户端进行处理

