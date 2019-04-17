# 使用 Conditions

需要注意

- Conditions 使用的是另一种类型的锁，与 Mutex 有细微的差别
- 等待 condition 的线程会保持阻塞，直到有另外的线程通知 condition 可用
- Condition 可能会发出虚假的信号通知 condition 可用，而实际上 condition 并不可用
    - 解决上面这个问题的方法是，将 Condition 和锁结合一起使用
- 使用 Condition 使用的是 `NSConditon` 类

## 使用方式

```objc
[cocoaCondition lock];
while (timeToDoWork <= 0)
    [cocoaCondition wait];
 
timeToDoWork--;
 
// Do real work here.
 
[cocoaCondition unlock];
```

- 其中，`cocoaCondition` 中包含了一个锁，以及 Condition 的数据

始终使用锁操作来包裹 Condition 的行为，如 `signal` 和 `wait`

```objc
[cocoaCondition lock];
timeToDoWork++;
[cocoaCondition signal];
[cocoaCondition unlock];
```

