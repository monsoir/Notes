# Realm 使用备忘

关于使用 Realm 的一些疑问解答 [👉 链接](https://stackoverflow.com/a/31594548/5211544)

## 为对象设置 primary key

重写 `Object.primaryKey()` 方法

## 写操作

向一个 realm 对象添加数据对象的操作，必须在一个 `write` 事务中进行，因为 `write` 事务会产生不可预计的耗时操作，因此，应尽量减少 `write` 事务

---

**`write` 事务是同步并阻塞的**

```
若 Thread A 开始了一个 write 事务，
Thread B 在 Thread A 结束前开始一个 write 事务
两者都在同一个 realm 对象上进行

那么，Thread A 必须完成 write 事务并提交后，Thread B 的 write 事务才能进行
```


## 添加对象

向 realm 添加完对象之后，可以继续使用这个对象，并且所有对这个对象的更改都会被存储（但必须是在同一个 `write` 事务中进行）

---

任何对对象作出的更改都可以在其他线程中访问到，只要：

1. 使用的是同一个 realm 对象
2. `write` 事务完成了

---

需要注意的是

`write` 操作会彼此阻塞，并且，当有多个 `write` 操作时，会阻塞其所在的线程

因此，推荐将 `write` 操作放置到一个独立的线程

---

当 `write` 操作进行时，`read` 操作并不会被阻塞

当对一个 realm 进行 `write` 操作时，所有其他有关这个数据对象的 realm 都会被通知到，并且作出更新


## 多线程

可以同时有任意数目的线程访问同一个 Realm 文件，并且由于每个线程都有对应的快照，因此线程之间绝不会产生影响

不能让多个线程都持有同一个 Realm 对象的实例

--- 

已归入管理的实例(managed)，若需要跨线程，需要使用 realm 提供的 [机制](https://realm.io/cn/docs/swift/latest/#section-26)


## Realm 对象

Realm 文件可以存储在硬盘或者内存上

- 硬盘上，使用 `init(path:)` 进行初始化
- 内存上，使用 `Configuraion` 进行配置

---

Realm 实例在内部的实现机制上，会进行缓存，因此，创建相同的 Realm 对象的消耗很小

> 相同的 Realm 对象是指：使用相同的文件路径或 identifer

---

若需要确保 Realm 实例彻底被销毁，则需要

- 在 `autoreleasepool {}` 块中使用 Realm 的 API
- 确保没有强引用指向 Realm

