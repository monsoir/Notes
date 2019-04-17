# 创建 Serial Dispatch Queue

使用 Serial Dispatch Queue, 你可以

- 对于资源竞争的任务，避免使用锁（锁会引发内核调用，we don't want that）
- 可以让任务按照我们的意愿次序执行
- 队列中的任务永不死锁

不同于 concurrent queue, 对于 serial queue, 要使用，需要自己创建，系统没有预定义。用户可以创建任意数量的 serial queue, 但要避免创建大量的 serial queue，企图在每个队列中塞一个任务，让任务可以并行执行，不要这样做！

当想要同时执行多个任务时，使用 concurrent queue, 这个是专业的。而创建 serial queue 时，需要明确这个 serial queue 的目的，如：避免数据竞争问题

创建一个 serial queue 的模版代码

```objc
dispatch_queue_t queue = dispatch_queue_create("com.example.MyQueue", NULL);
```

