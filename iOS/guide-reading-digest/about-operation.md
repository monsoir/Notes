# 关于 Operation

## 是什么

- NSOperation 是一个抽象类，只能通过继承它后才能正常工作
- 作为一个抽象类，NSOperation 实际上已经封装了大量作为基础设施的工作
- Foundation 本身提供了两个 NSOperation 的子类，可以直接使用

## Foundation 中关于 Operation 的子类

- NSInvocationOperation
- NSBlockOperation

### NSInvocationOperation

构建如此一个 operation, 需要

- 一个作为执行者的对象
- 一个作为执行逻辑的方法

### NSBlockOperation

构建如此一个 operation, 需要一个或多个 block, 即一个 `NSBlockOperation` 对象，其中包含一个或多个 block 对象

当一个 `NSBlockOperation` 对象中包含多个 block 对象时，这若干个 block 会使用「组原语」(group semantic)进行修饰，这会使得

- 这若干个 block 会并行执行
- 只有当这若干个 block 都完成了，这个 `NSBlockOperation` 才算是完成

## 关键特性

所有的 `NSOperation` 对象都支持一下特性

- 支持构建不同 operations 之间的依赖关系
- 支持提供一个完成回调，这个回调会在 operation 的主任务完成后调用
- 支持使用 KVO 监听 operation 的执行状态
- 支持设置 operations 的优先级
- 支持取消

## 使用须知

### Operation 既可以不在 queue 中执行，也可以在 queue 中执行

执行一个 operation 可以有两种操作

- 将 operations 添加到一个 operation queue 中
- 手动调用 operations 的 `start` 方法

对于手动调用 `start` 方法这种方式，是**不保证** operations 会与同一线程中的其他代码实现并行执行。其中 `isConcurrent` 方法可以查看 operations 是否为并行执行。默认为 `NO`, 这意味着这个 operation 在当前线程是以**同步**的方式执行

### 一般情况下，operations 都是异步执行

大多数情况下，开发者都不需要自己构建 concurrent operations

只要我们记得将 operations 添加到某个 operation queue 中的话，operations 就会以异步方式执行

> 当一个非并行的(nonconcurrent) operation 添加到某个 operation queue 时，这个 operation queue 实际上会使用另外一个线程来执行这个 operation, 所以最后还是异步执行

只有在需要异步执行一个 operation, 而又没有将它添加到任何一个 operation queue 时，才需要自己构建一个 concurrent operation

