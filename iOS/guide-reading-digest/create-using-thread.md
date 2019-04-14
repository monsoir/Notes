# 使用 NSThread 创建线程

使用 `NSThread` 创建线程，有两种方法

- 使用 `detachNewThreadSelector:toTarget:withObject:` 方法
- 创建一个 `NSThread` 对象，调用其 `start` 方法 (只在 iOS 和 OS X v10.5 及以后支持)

## detached 线程

这两个方法都会创建一个 detached 线程

> 什么是 detached 线程？
> 
> detached 线程的资源会在其 exit 时，被自动被系统回收(automatically reclaimed)

## 线程对象的进化

### 初级线程对象

在 `NSThread` 之前的版本中，当需要获取一个 `NSThread` 对象或访问其属性时，都需要在线程开始运行后才能进行，即 `detachNewThreadSelector:toTarget:withObject:` 方法。

### 进化后的线程对象

而在 OS X v10.5 及以后，支持在创建 `NSThread` 对象时，不会立刻创建对应的线程，这就使得在线程开始运行前，对线程进行配置

> 「创建 `NSThread` 对象时，不会立刻创建对应的线程」听起来可能有点怪
> 
> 可以这样理解，`NSThread` 对象可以看作是一个数据结构，存放数据的地方，而线程这个我们看不见的东西才是真正执行的东西，线程执行的过程中会不断从 `NSThread` 对象中获取需要的数据

因此，使用第二种方法创建线程，我们就可以对线程数据进行配置后再触发线程的创建与运行

```objc
NSThread* myThread = [[NSThread alloc] initWithTarget:self
                                        selector:@selector(myThreadMainMethod:)
                                        object:nil];
[myThread start];  // Actually create the thread
```

---

同时，还有一种另类的创建线程对象的方法

1. 继承 `NSThread`
2. 重写 `main` 方法，在其中配置线程执行入口

