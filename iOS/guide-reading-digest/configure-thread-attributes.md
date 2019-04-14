# 配置 NSThread 属性

## 配置线程的栈大小

- 在 Cocoa 体系内，即使用 `NSThread` 对象，使用 `setStackSize` 方法来设置线程对象的栈大小
- 在 POSIX 体系内
    1. 创建一个 `pthread_attr_t` 结构体指定栈大小
    2. 使用 `pthread_attr_setstacksize` 方法，将 1 中的结构体替换默认的栈大小
    3. 或者，在创建时，调用 `pthread_create` 时传入 1 中的结构体

## 配置线程内存储空间大小

每个线程内部都会维护一个字典。这个字典存储了线程执行过程中所需要的数据

而 Cocoa 体系与 POSIX 体系使用不同的方法存储这个字典，因此，修改时也需要使用不同的方法

- 在 Cocoa 体系中
    1. 使用 `NSThread` 的实例方法 `threadDictionary` 获取到一个 `NSMutableDictionary` 对象
    2. 对上面获得的对象添加需要的键值对 

- 在 POSIX 体系中，使用 `pthread_setspecific` 设置字典，使用 `pthread_getspecific` 获取字典

## 配置线程的 detached 状态

大多数高级的线程技术都是默认创建 detached 线程，就是那种运行完成之后，系统自动回收其资源

但还有另外一种线程，joinable 线程，这里直译一下就是可接入的线程（这翻译随便理解一下就算了）

一般来说，对于 joinable 线程，就算在它运行完成之后，系统也不会自动回收它的资源，而是要等到有另外一个线程 join 了之后，系统才会去回收

于是，对于 joinable 线程，一个简化的工作流程是这样的，假设 joinable 线程为 A, 另外一个 join 的线程为 B

1. A 执行完成，即将 exit, 调用 `pthread_exit` 并将返回的数据传到这个函数中，但此时，系统并不会去回收 A 的资源，因为还没有另外一个线程 join
2. B 来了，通过调用 `pthread_join` 函数，将 A 传递出来的数据拿到，此时 B 继续执行，而现在，系统可以去回收 A 的资源了

于是，我们可以清晰可以看到，joinable 线程实际上是在运行结束后，还有等到其产生的数据有其他线程认领了之后，才算是真正的完成，被系统回收资源

而唯一可以创建 joinable 线程的方法是使用 POSIX 线程，而 POSIX 线程模型，默认创建的就是 joinable 线程。

改变 detached 状态

- 在线程开始前，调用 `pthread_attr_setdetachstate` 函数改变
- 在线程开始后，调用 `pthread_detach`函数改变

## 配置线程优先级

- 在 Cocoa 体系中，使用 `NSThread` 的实例方法 `setThreadPriority:`
- 在 POSIX 体系中，使用 `pthread_setschedparam` 函数


