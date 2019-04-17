# 获取 Global Concurrent Dispatch Queue

当需要多个任务并行执行时，那么就适合使用 global concurrent dispatch queue 了，当然，并行执行的任务数量，最终还是收到了操作系统的限制

操作系统为每个 App 提供了四种 global concurrent dispatch queue, 这四种 queue 的不同点只在于它们的优先级大小

由于这四种 queue 是事先存在了，所以我们直接向系统拿就行了

```objc
// 默认
dispatch_queue_t aQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

// 高
dispatch_queue_t aQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0);

// 低
dispatch_queue_t aQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_LOW, 0);

// 后台
dispatch_queue_t aQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0);
```

虽然上面四种队列都是引用计数的对象，但由于它们是全局队列，因此，我们并不需要关心它们的内存管理，用就是了

第二个参数 0 其实是为了以后扩展使用的，现在直接传个 0 就可以了

