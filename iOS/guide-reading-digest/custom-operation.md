# 自定义 Operation

当 Foundation 中内置的两个 Operation 子类不能满足要求时，可以考虑创建自定义的 Operation 子类

自定义 Operation 子类又分为两种类型

- nonconcurrent
- concurrent

自定义 nonconcurrent 的子类很简单，只需要做好以下工作，其他工作父类 `NSOperation` 都做好了

- 实现主任务的逻辑
- 响应 cancellation 的事件

自定义 concurrent 的子类就麻烦一些，**还需要**做的是

- 覆盖部分父类的基础设施代码

## 实现主任务的逻辑

对于每个 `NSOperation` 类来说，都至少需要实现以下两个方法

- 一个自定义的初始化方法
- `main` 方法

```objc
@interface MyNonConcurrentOperation : NSOperation
@property id (strong) myData;
-(id)initWithData:(id)data;
@end
 
@implementation MyNonConcurrentOperation
- (id)initWithData:(id)data {
   if (self = [super init])
      myData = data;
   return self;
}
 
-(void)main {
   @try {
      // Do some work on myData and report the results.
   }
   @catch(...) {
      // Do not rethrow exceptions.
   }
}
@end
```

补充：

- 上述实现只是针对这一部分设计到的方法进行了实现而已
- 实际上，我们可以将 `NSOperation` 类看作是一个任务的模型，是任务的可视化部分，而真正的任务才是看不见的部分。也可以类比 ES6 中的「代理对象」，`NSOperation` 更多是不可视任务的可视化部分

## 响应 cancellation 的事件

为什么要响应 cancellation 事件？

> 如果任务在执行过程中突然中断，又没有响应 cancellation 事件的话，那么任务所申请的资源就无法释放

那么响应 cancellation 事件是如何实现？

> 响应突然的中断听起来很 magic 一样，开发者是怎么知道任务会在哪里中断呢？实际上，开发者并不知道，靠的全是瞎猜。
> 在可能中断的地方调用 `isCancelled` 方法进行判断 >_<

响应 cancellation 事件，靠的就是间歇性调用 `isCancelled` 方法进行判断，同时 `isCancelled` 方法是非常轻量级的，可以频繁调用而无需考虑性能问题

开发者应该在以下阶段调用 `isCancelled` 判断是否需要中止任务

- 在开始真正的任务工作前
- 在任务中的每一个循环的开始，或者，如果每个循环耗时比较大，那就在每个循环的中调用多次 `isCancelled` 进行判断
- 估计每一个容易产生中断的地方，调用 `isCancelled` 进行判断

```objc
- (void)main {
   @try {
      BOOL isDone = NO;
 
      while (![self isCancelled] && !isDone) {
          // Do some work and set isDone to YES when finished
      }
   }
   @catch(...) {
      // Do not rethrow exceptions.
   }
}
```

上述代码并没有实现资源回收，实际中开发者应该注意资源回收的代码实现

## 针对自定义 concurrent （还需要）的实现

`NSOperation` 对象默认是同步执行的。但在 [关于 Operation](./about-operation.md) 中提及过，只要把 operation 放到某个 operation queue 中，那么这个 operation 就可以异步执行了，不管它是 concurrent 还是 nonconcurrent

当然，如果想要手动执行自定义的 operation, 并且希望它还是能够异步执行的话，就需要做更多的工作。以下是实现 concurrent 时可能需要重写的方法

- required
    - `start`
    - `isExecuting`
    - `isFinished`
    - `isConcurrent`
- optional
    - `main`


### start

所有 concurrent operations 都需要重写此方法。手动执行 operation, 靠的就是调用 `start` 方法

因此，重写的 `start` 方法逻辑，应该是创建任务执行时的线程或运行环境。在这部分的逻辑中，不能再调用 `super`

### isExecuting 和 isFinished

这两个方法的意义在于，要自己维护任务的内部执行状态，因为任务的执行环境是自定义的，如 `start` 方法所述。每当任务状态需要发生改变时，都需要记得修改这两个状态的值

在修改两个状态值时，应注意

- 保证线程安全
- 记得发出 KVO 通知，维护状态可以 KVO 的特性

### isConcurrent

用于标记 operation 是可并行执行的，重写时只需要简单返回 `YES`

### main

这个方法主要用于编写任务的逻辑。当然，对于 concurrent operations 来说，任务的逻辑实际上是可以在 `start` 方法中编写。但将任务逻辑搬移到 `main` 方法中，可以更好地将关注点分离，使得代码结构更加清晰（`start` 方法主要是用来创建任务的执行环境）。

```objc
@interface MyOperation : NSOperation {
    BOOL        executing;
    BOOL        finished;
}
- (void)completeOperation;
@end
 
@implementation MyOperation
- (id)init {
    self = [super init];
    if (self) {
        executing = NO;
        finished = NO;
    }
    return self;
}
 
- (BOOL)isConcurrent {
    return YES;
}
 
- (BOOL)isExecuting {
    return executing;
}
 
- (BOOL)isFinished {
    return finished;
}

- (void)start {
   // Always check for cancellation before launching the task.
   if ([self isCancelled])
   {
      // Must move the operation to the finished state if it is canceled.
      [self willChangeValueForKey:@"isFinished"];
      finished = YES;
      [self didChangeValueForKey:@"isFinished"];
      return;
   }
 
   // If the operation is not canceled, begin executing the task.
   [self willChangeValueForKey:@"isExecuting"];
   [NSThread detachNewThreadSelector:@selector(main) toTarget:self withObject:nil];
   executing = YES;
   [self didChangeValueForKey:@"isExecuting"];
}

- (void)main {
   @try {
 
       // Do the main work of the operation here.
 
       [self completeOperation];
   }
   @catch(...) {
      // Do not rethrow exceptions.
   }
}
 
- (void)completeOperation {
    [self willChangeValueForKey:@"isFinished"];
    [self willChangeValueForKey:@"isExecuting"];
 
    executing = NO;
    finished = YES;
 
    [self didChangeValueForKey:@"isExecuting"];
    [self didChangeValueForKey:@"isFinished"];
}
@end
```


