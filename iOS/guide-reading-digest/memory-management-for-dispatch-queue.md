# Dispatch Queues 的内存管理

## 都是引用计数的对象

Dispatch Queues 和其他 Dispatch 相关的对象，都是引用计数的对象类型，使用 `dispatch_retain` 和 `dispatch_release` 来增加或减少对象的引用计数

## 保证队列不被回收

对于某些任务使用的队列，如果这个这个队列是传递过来的参数，那么正确的做法是先对这个队列进行增持的操作，确保任务在运行期间，队列不会被系统回收

## 不需要担心内存管理的 Dispatch 对象

对于 global dispatch queues, 用户就不需要 retain 或 release 队列对象了。当然，用户也可以调用 retain 或 release 方法，不过，这种调用会被忽略而已

这些 global dispatch queues 包括：concurrent dispatch queues, main dispatch queue

