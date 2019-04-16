# 什么时候会用到 Run Loop

当需要使用到 Run Loop 时，其线程通常会叫做 `secondary threads`, 这个我也不知道中文叫什么好了

当遇到下面的情况时，使用 Run Loop 会比较好

- 使用 port-based 或自定义的 input sources 与其他线程通信
- 在线程上使用到 timers
- 在 Cocoa 程序中用到了 `performSelector...` 之类的方法（这个指的应该是 macOS 上的 App）
- 需要持续维护一些线程来执行周期性的任务

当需要执行一些耗时的，并且已经决定好去向(predetermined)的一些任务，其实应该尽量避免使用 Run Loop

