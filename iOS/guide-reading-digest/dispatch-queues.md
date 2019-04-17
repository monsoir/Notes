# Dispatch Queues

GCD 内置了三种 dispatch queue, 当然，用户可以根据需要创建自己的 dispatch queue

- Serial
- Concurrent
- Main

## Serial

串行队列，一次只执行一个任务，又叫 private dispatch queue

在这个队列上执行的某个任务，只会在某一个线程上执行（一个任务对应一个线程，不同的任务可能会在不同的线程上执行），而这个线程由这个串行队列管理

通常用于避免某个资源竞争访问

用户也可以自己创建若干个串行队列，而且这若干个串行队列中的任务是并行执行的

> 即对于某一个串行队列 S 来说，S 中含有任务 s1, s2, s3, 那么，s1, s2, s3 是一个接一个串行执行；
> 
> 但对于若干个串行队列 A, B, C, 它们分别含有任务 (a1, a2, a3), (b1, b2, b2), (c1, c2, c3)
> 那么 a1, a2, a3 之间是一个接一个串行执行，b1, b2, b3 之间是一个接一个串行执行，c1, c2, c3 之间是一个接一个串行执行
> a1, b1, c1 之间是并行执行

## Concurrent

又叫 global dispatch queue

与 Serial 类似，但这个队列会同时运行若干个任务，至于同时运行的任务数量，就取决于系统的条件

在 iOS 5 及以后，用户可以通过指定 `DISPATCH_QUEUE_CONCURRENT` 参数来创建自己的 concurrent queue

而系统中预定义了四种全局的 concurrent queues 供用户使用

## Main

main dispatch queue 是一种特殊的 serial queue, 在应用的主线程上执行任务


