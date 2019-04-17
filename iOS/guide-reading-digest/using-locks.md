# 使用锁

## 使用 Mutex

要使用 Mutex, 步骤很简便

1. 声明一个和初始化 `pthread_mutex_t` 的变量
2. 使用 `pthread_mutex_lock` 上锁，使用 `pthread_mutex_unlock` 解锁
3. 使用 `pthread_mutex_destroy` 释放锁

使用 Mutex 模版

```objc
pthread_mutex_t mutex;
void MyInitFunction()
{
    pthread_mutex_init(&mutex, NULL);
}
 
void MyLockingFunction()
{
    pthread_mutex_lock(&mutex);
    // Do work.
    pthread_mutex_unlock(&mutex);
}
```

## 使用 NSLock

`NSLock` 实际上时实现了 `NSLocking` 协议的类，其中定义了 `lock` 与 `unlock` 方法

除此以外，`NSLock` 还额外添加了 `tryLock` 和 `lockBeforeDate:` 两个方法

- `tryLock` 尝试获取锁，当锁不可用时，不会阻塞，而是返回 `NO`
- `lockBeforeDate:` 在规定的时间内，阻塞并尝试获取锁，当锁不可用时，取消阻塞并返回 `NO`

```objc
BOOL moreToDo = YES;
NSLock *theLock = [[NSLock alloc] init];
...
while (moreToDo) {
    /* Do another increment of calculation */
    /* until there’s no more to do. */
    if ([theLock tryLock]) {
        /* Update display used by all threads. */
        [theLock unlock];
    }
}
```

## 使用 @synchronized 指令

是 Objective-C 中简便的直接创建 Mutex 的方式，工作方式与 Mutex 一样

```objc
- (void)myMethod:(id)anObj {
    @synchronized(anObj) {
        // Everything between the braces is protected by the @synchronized directive.
    }
}
```

- `@synchronized` 中的对象，是识别 Mutex 锁的一个标识符，每个标识符对应一个 Mutex
- `@synchronized` 是会发生异常的，在异常发生时，相关的 Mutex 锁会被释放，然后向程序抛出一个异常，因此使用时，应注意对 `@synchronized` 的异常处理

## 使用 NSRecursiveLock

```objc
NSRecursiveLock *theLock = [[NSRecursiveLock alloc] init];
 
void MyRecursiveFunction(int value)
{
    [theLock lock];
    if (value != 0)
    {
        --value;
        MyRecursiveFunction(value);
    }
    [theLock unlock];
}
 
MyRecursiveFunction(5);
```

- 要注意的是，`lock` 了 n 次，就要 `unlock` n 次，这样子，递归锁才会彻底被释放掉

## 使用 NSConditionLock

要注意几点

- `NSConditionLock` 实际上还是使用 Mutex
- 与 Conditions 行为相似，但实现与 Condition 完全不同
- `NSConditionLock` 中的 condition 实际上只是一个 `NSInteger` 类型的数据

利用 `NSConditionLock` 实现「生产者-消费者」

生产者

```objc
id condLock = [[NSConditionLock alloc] initWithCondition:NO_DATA];
 
while(true) {
    [condLock lock];
    /* Add data to the queue. */
    [condLock unlockWithCondition:HAS_DATA];
}
```

消费者

```objc
while (true) {
    [condLock lockWhenCondition:HAS_DATA];
    /* Remove data from the queue. */
    [condLock unlockWithCondition:(isEmpty ? NO_DATA : HAS_DATA)];
 
    // Process the data locally.
}

```

