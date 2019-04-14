# Threading Packages

线程大礼包

所有线程的底层实现都是依赖 Mach Thread, 它提供了线程的基本的基本操作

- 抢占式执行 preemptive execution
- 线程调度，独立执行

包括但很少，几乎不会直接在 Mach 层面使用到，而是使用其衍生出来的线程技术

## Cocoa threads

使用 `NSThread`

通过 `NSThread` 产生一个线程，如

```objc
[myObj performSelectorInBackground:@selector(doSomething) withObject:nil];
```

## POSIX threads

提供的是 C 语言接口来创建线程

## Multiprocessing Services

这是很老的技术了，只在 Mac OS 上可用



