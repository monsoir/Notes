# Conditions

## 工作原理

- Condition 是另外一种信号量，它使得在某个特定的 condition 可用时，线程间可以相互通知
- 当一个线程 A 测试一个 condition 时，如果 condition 不可用，那么 A 会保持阻塞；直到其他线程通知 condition

## Condition 与 Mutex 的区别

- 同一时刻可以有多个线程获取到 Condition, 即同一时刻可以有多个线程获取到受保护的资源
- Condition 的角色更多是像一个看门人(gatekeeper), condition 根据某些标准来决定是否允许一个或多个线程访问受保护的资源

## 适用于

Condition 可用于 

- 保证资源的使用安全
- 确保任务按一定次序执行



