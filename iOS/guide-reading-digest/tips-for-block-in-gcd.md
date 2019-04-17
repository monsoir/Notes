# 在 GCD 中使用 block 的须知

在使用 CGD 时，任务都是通过 block 来进行封装，但使用 block 的时候，需要注意

- 不要应用体积比较大的数据结构，不要引用那些在 block 执行时，其对象会被释放的对象
- 当 block 添加到 GCD 时，GCD 会将 block 进行复制；同时 block 执行完毕后，CGD 会自动释放 block. 用户无需关心 block 的添加与释放
- 由于在添加 block 时，GCD 还是要复制一份，因此，并不是所有任务都适合使用 GCD
- 不要缓存与底层线程相关的数据，不要奢望数据可以从另一个 block 中获取；要想实现数据共享，使用 dispatch queue 中的 `context`
- 如果 block 中创建了大量的 Objective-C 对象，那么最好使用 `@autorelease` 对 block 进行内存管理。虽然 GCD 中的队列自身也有自动释放池，但我们并不知道这个池什么时候会清空，因此，对于内存敏感的程序，还是需要注意一下

