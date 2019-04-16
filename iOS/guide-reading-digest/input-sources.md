# Input sources

## 是什么

Input sources 是异步地传递事件到线程中

## 两种类别

Input sources 又分为两种类型

- Port-based input sources
- Custom input sources

对于 Run Loop 而言，Input sources 是哪种类型并不重要，都是一样的处理。两种类型的 input source 的不同之处在于触发的方式的不同

### Port-based input sources

- 监听的是应用 Mach 端口
- 由内核自动触发

在 Core Foundation 中，创建一个 source, 需要创建一个 port 实例，与这个 port 实例关联的 Run Loop source

### Custom input sources

- 监听的是自定义的事件
- 从另一个线程手动触发

在 Core Foundation 中，创建一个 source, 需要使用与 `CFRunLoopSourceRef` 类型相关的函数，并需要通过一系列的回调函数对 input source 进行配置。这些配置包括了如何处理事件，当 source 从 Run Loop 中移除时，如何清理资源



