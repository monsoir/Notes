# 类的初始化方法

## load

调用时机

- 当一个类或类别被加载到 Runtime 时调用

做什么

- 实现 `load`, 执行一些需要在类层面的工作，如 Method Swizzling

注意

- 如果一个类 ClassA 中实现 `load` 方法，ClassA 的类别 ClassA(ACategory) 也实现了 `load` 方法，那么，ClassA 和 ClassA(ACategory) 中的 `load` 方法都会被调用，即不存在覆盖的问题
- 同时，对于实现了 `load` 方法的类或类别，不管它们是否有在其他类中引入并使用，在加载时，都会执行 一次 `load` 方法
- 一个类的 `load` 方法，会在它所有的父类的 `load` 调用后才调用
- 一个类别的 `load` 方法，会在其类的 `load` 调用后才调用

## initialize

调用时机

- 当类接收到第一条消息前

做什么

- 常用于初始化静态类变量，同时最好配合单例模式进行使用

注意

- `initialize` 方法时线程安全的
    - 即执行 `initialize` 时，如果在其他线程需要访问到这个类，那么，访问操作将会被阻塞到 `initialize` 执行结束
    - 因此，不要在 `initialize` 执行繁重的任务
- 父类会在子类之前调用 `initialize` 方法
- 实际上，当实现 `initialize` 时，需要进行额外的类型检查，避免一个类中的 `initialize` 被多次调用

```objc
+ (void)initialize {
  if (self == [ClassName self]) {
    // ... do the initialization ...
  }
}
```

> 为什么 `initialize` 被调用两次？
>
> 有父类 ClassA 与 ClassB
>
> 情况一：ClassA 实现了 `initialize`, ClassB 没有实现 `initialize`
> 第一次调用：向 ClassA 发送第一次消息
> 第二次调用：向 ClassB 发送第一次消息，由于 ClassB 没有实现 `initialize`, Runtime 会自动调用其父类 `ClassA` 的 `initialize` 方法
> 
> 情况二：ClassA 实现了 `initialize`, ClassB 也实现了 `initialize`, 并且在 ClassB 中显示调用了父类方法 `[super initialize]`
> 第一次调用：向 ClassA 发送第一次消息
> 第二次调用：向 ClassB 发送第一次消息，由于 ClassB 显示调用了父类方法，于是又调用了 ClassA 的 `initialize`



