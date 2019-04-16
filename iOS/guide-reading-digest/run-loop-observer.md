# Run Loop observer

可以对 Run Loop 的执行阶段进行监听

## 可监听的阶段

- 进入某个 Run Loop
- Run Loop 将要执行某个 Timer 事件
- Run Loop 将要执行某个 Input source 事件
- Run Loop 将要休眠
- Run Loop 由于要处理某个事件醒来了，但还没有处理该事件的时候
- 退出某个 Run Loop 时

## 创建一个 observer

使用的是 `CFRunLoopObserverRef` 类型，通过回调函数的方式来监听感兴趣的事件

## 一次性或重复性

与 Timer sources 类似，observer 可以是一次作用或重复多次作用

当 observer 是一次作用时，会在触发执行完成后，自动从 Run Loop 移除

