# 线程保活

[Demo](https://github.com/Monsoir/thread-runloop-demo)

## 背景
 
- 在自己的项目中，需要用户输入关键词，找出与关键词匹配的数据，同时，对数据中的关键词进行高亮
- 同时，每当用户的输入都发生变化时，都需要重新匹配查找结果
- 而在高亮操作中，需要查找字符串中所有匹配的子字符串，这就很有可能需要很大的工作量，放在主线程中操作的话，很有可能会堵塞 UI, 因此，需要一个字线程来做这个事

---

- 在自己的项目使用了 Realm 作为数据库
- 而在 Realm 中，Realm 的查询结果 `Results` 是不可以跨线程工作的，即对 `Results` 中的每条数据的读取，始终只能在一条线程中操作

---

- 对于一般的读操作，我之前的做法，是在子线程中进行查询，然后通过 Realm 官方提供的跨线程保护，将 `Result`, 将 `Result` 转换为可以在主线程读取的对象，一切安好

**实际上，上面的跨线程操作能够顺利进行，一个重要的原因是：主线程全局只有一个**

回想一下，当我们需要将 UI 操作回传给主线程是怎么操作的呢？

```swift
DispatchQueue.main.async {
    // do something related to UI
}
```

实际上，我们并不是直接把 UI 操作给到主线程，而是给到了队列 `main`, 通过 `main` 队列将任务派发给主线程。所以，我们并不是直接面向线程进行操作，直接面向的是队列。而队列的背后，实际上是由若干条线程，等着任务

当我下意识将 `Results` 交给 `main` 队列时，Realm 从来就没有报错，根据 `Results` 不能跨线程的特点，就只有一个可能，`main` 队列所设计的线程，就只有一条（否则，当主队列派发 `Results` 相关任务时，必定会出现跨线程错误）

综上

- 将查询 `Results` 的任务，查找关键词子字符串的任务，两个任务必须都在同一条线程上
- 将两个任务交给某一个系统维护的全局并发队列，或者串行队列，都是不可行的。因为这些队列的背后，有一个系统维护的线程池，其中有若干条线程。系统每次调度任务，就会从线程池中拿出一条线程，用来执行任务，并不能保证两个任务的线程是一致
    - 即使是串行队列，也只是保证任务被串行调度，而不是保证同一个线程

---

因此，将查询与查找两个任务放在同一个线程中进行确定下来了。接下来，还需要明确一下线程的工作方式

- 一般情况下，线程只是一次性的，即执行完任务，这个线程就完了，就在也不能继续执行其他任务了。每个线程都有维护一个状态，当线程执行完任务之后，这个状态就为 `finished`
- 实际上，我觉得 thread 在这里翻译成「线程」有点误导（是一条线吗？一个路径吗？），感觉意译为「执行器」可能更容易接受

由于线程一次性的特点，所以当用户的输入发生改变时，进行搜索和匹配任务前，都要重新创建一个线程, 是非常浪费资源的。所以，我们需要重用线程，至少在用户还留在搜索界面上时，用的都是同一个线程

在 iOS 上，重用线程的操作，就需要依靠线程保活，即线程可以持续接受并执行任务

## 如何实现线程保活

线程保活，需要知道线程的一个基础设施，即 RunLoop

简单来说，RunLoop 就是维持线程状态不转为 `finished` 的工具。RunLoop 在它活着的期间，会一直以低耗能地进行死循环（相对于使用 `while true` 的高效能死循环），当接受到任何消息时，它就会起来工作了

RunLoop 是需要我们自己创建的，而创建 RunLoop 实例，需要一个 port, 可以想象成，这个 port 就像一个钩子，把 RunLoop 实例与应用拉上了关系

最后，我们将新创建的 RunLoop 与自己管理的线程拉上关系，从此，就实现了线程保活

线程保活的三板斧：

- 创建 Port
- 创建 RunLoop
- 创建 Thread


## 实现线程保活

我们需要声明 3 个属性

```swift
private var aliveThreadRunloop: RunLoop? // 保活用的 RunLoop
private var aliveThreadRunloopPort: NSMachPort? // 挂靠 RunLoop 的 port
private var aliveThread: Thread? // 需要活着的线程
```

---

创建线程

```swift
let t = Thread(target: self, selector: #selector(asyncRun), object: nil)
t.name = "alive"
t.start()
aliveThread = t
```

- 创建线程时的 `asyncRun`, 是维持线程活着的重要方法
- 给线程一个名字，方便调试时使用
- 记得要调用 `start`, 否则线程永远不会运行起来，`asyncRun` 方法也不会运行起来

---

保活

```swift
@objc private func asyncRun() {
    autoreleasepool { () -> Void in
        guard let aliveThread = aliveThread else { return }
        
        print("hello, can you here me, \(Thread.current.name ?? "unknown") thread")
        
        aliveThreadRunloop = RunLoop.current // 1
        
        aliveThreadRunloopPort = NSMachPort() // 2
        aliveThreadRunloop?.add(aliveThreadRunloopPort!, forMode: .common)
        
        shouldKeepRunning = true // 3
        while shouldKeepRunning && aliveThreadRunloop!.run(mode: .default, before: Date.distantFuture) {} // 4
        
        // ---- 分界线 ----
        
        if Thread.current != aliveThread {
            fatalError("Current thread is \(String(describing: aliveThread.name))")
        }
        
        // 6
        
        // 移除 port
        if let runloopPort = aliveThreadRunloopPort {
            aliveThreadRunloop?.remove(runloopPort, forMode: .common)
        }
        
        // 移除 RunLoop
        if let runloop = aliveThreadRunloop {
            CFRunLoopStop(runloop.getCFRunLoop())
        }
        
        // 停止线程运作
        if !aliveThread.isCancelled {
            aliveThread.cancel()
        }
    }
}
```

1. 将创建一个 RunLoop 实例，我觉得 `current` 是不是命名出现问题了？名字给人的感觉好像就是返回现有的 RunLoop. 实际上不是，这会为当前线程创建一个 RunLoop (如果没有)，文档的 API 说明：

    ```
    If a run loop does not yet exist for the thread, one is created and returned.
    ```
    
    - 同时要注意一点是，这个方法的执行是异步的，所以在运行这个方法时，其所在的线程一定是 `alive` 这个线程

2. 创建一个 port, 并将其添加到新创建的 RunLoop 实例中，并指定 Mode, `common` 覆盖了开发者可以接触到的，经常会切换到的 mode
    - 处理使用 port 挂靠 RunLoop, 还可以使用 timer

3. 我们将会创建一个短期内可存活的线程，这个 `shouldKeepRunning` 作为标记，表明了线程是否可以存活
4. 启动新创建的 RunLoop 实例，到这里，就会发生上面所说的低耗能死循环，而「分界线」下面的代码就暂时不会运行
    - 启动 RunLoop 有多个方法
        - 最简单的 `func run()`, 但以这种方式启动的 RunLoop, 保证可以永久运行，但不保证可以停止运行
        
            ```
            Manually removing all known input sources and timers from the run loop is not a guarantee that the run loop will exit. 
            
            If you want the run loop to terminate, you shouldn't use this method
            ```
            
        - `func run(until limitDate: Date)`, 这种方式启动的 RunLoop, 可以保证一直运行，直到超时时间到了。同样地，以这种方式启动的 RunLoop, 也不保证可以手动停止运行

            ```
            Manually removing all known input sources and timers from the run loop is not a guarantee that the run loop will exit.
            ```
            
        - `func run(mode: RunLoop.Mode, before limitDate: Date) -> Bool`, 这种方式启动的 RunLoop, 可以同时制定 mode, 但同样地，保证可以手动停止运行
        
            ```
            Manually removing all known input sources and timers from the run loop does not guarantee that the run loop will exit immediately. 
            ```

5. 停止 RunLoop, 即线程可以结束了，我们需要将 `shouldKeepRunning` 设置为 `false`, 要注意的是，设置的时候最好是要在同一个线程中设置，防止多个线程同时读写一个变量
    
    ```swift
    @IBAction func handleStopAliveThread(_ sender: Any) {
        guard let aliveThread = aliveThread else { return }
        perform(#selector(asyncStop), on: aliveThread, with: nil, waitUntilDone: false)
    }
    
    @objc private func asyncStop() {
        shouldKeepRunning = false
    }
    ```

6. 当 `shouldKeepRunning` 为 `false` 时，此时就会执行到了「分界线」后面的代码，此处我们将线程取消，并将之前创建的 RunLoop 对象中的 port 移除，并停止 RunLoop 运转

### Mark

- 当我们需要调用线程执行任务时，可以

    ```swift
    perform(#selector(printSomethingOnAliveThread), on: aliveThread, with: nil, waitUntilDone: false)
    ```
    
    对于上面的方法，文档上还有描述是
    
    > This method queues the message on the run loop of the target thread using the default run loop modes—that is, the modes associated with the common constant. As part of its normal run loop processing, the target thread dequeues the message (assuming it is running in one of the default run loop modes) and invokes the desired method.
    
    当我们通过 `perform` 这个方法向另外一个线程(A)发起任务时，这个方法会在 A 所在的 RunLoop(R)(并且是在 `RunLoop.Mode.Common` 这个 mode 下), 将消息压入队列
    
    之后，当 R 运转时，并从队列中拿出刚才压进队列的消息，在线程 A 上执行方法

- 实际上，一个线程，开启了之后，就会一股脑筋地向 `finished` 这个状态（终点）运行。这里开启的 RunLoop 的作用：
    - 实际上也相当于让线程不断地在原地打转，阻止线程到达终点，不过用 RunLoop 的方式可以让消耗减到最低
    - 让线程可以接受到任务并执行
    - 如果直接使用 `while true` 进行死循环的话，这就真的是死循环了。真，在于是 `while` 是占用 CPU 的，可以看到 CPU 占用几乎是上到 100%. 而 `run(mode: RunLoop.Mode, before limitDate: Date)` 是有返回值的

