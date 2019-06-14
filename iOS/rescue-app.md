# 抢救应用

[Demo](https://github.com/Monsoir/thread-runloop-demo)

## 程序是如何崩溃的
    - 程序崩溃，实际上是 **unexpected termination** , 当一个程序接受到一个未被处理的信号(unhandled singnal)时，就发生了崩溃

## Unhandled signal 的来源，有
    - 内核 the kernel
    - 其他进程 other processes
    - 应用本身 application itself

## 最常引起崩溃的 singal
    - `EXC_BAD_ACCESS`
        - 当应用试图访问应用不允许访问的内存时发生，如访问已经被释放的对象
        - 属于 Mach 异常，由内核发送给应用的 signal, 当 Mach 层面没有处理这个 signal 时，这个 signal 会被转换为 `SIGBUS` 或 `SIGSEGV` BSD signal
    - `SIGABRT`
        - 属于 BSD 异常，是应用发给自身的 signal
        - 当 `NSException` 或 `obj_exception_throw` 未被捕捉处理时发生
- 不能兜底的异常
    - `SIGKILL` 这是由命令行中的 `kill -9` 发出的信号
    - `SIGSTOP` 这是由命令行中 ctrl+z 发出的信号
- 兜底未处理的异常的方法
    - 使用 `NSUncaughtExceptionHandler` 函数， 为 Objective-C 异常注册一个处理程序
    - 使用 `signal` 函数，为 BSD signals 注册处理程序
- 兜底异常方法的原则
    - 处理方法永不返回
        - 由于引起异常所在的上下文已经不正常了，所以，返回原来的上下文是不恰当的做法
        - 由于方法不返回，而是直接进入另一个接管的 RunLoop, 因此，引起异常线程所占用的调用栈内存将会永远被占用着，即内存泄漏了。这是代价
    - 处理方法恢复原来的 RunLoop
        - 由于我们需要创建新的上下文来接管原来的上下文，在 iOS 应用中，即一个新的 RunLoop. 此时我们需要创建一个新的 RunLoop 来代替原来的 RunLoop. 同时，还需要复原原来 RunLoop 中的所有 mode
            - 有些 mode 是私有的，我们并不能获取，但系统提供方法来一次性获取所有的 mode
    - 获取到栈信息
    - 保存崩溃信息

## 抢救应用实现

为了实现抢救应用，我们需要在全局范围内注册一个处理程序，用于兜底，如在 `AppDelegate` 中进行注册

接着，需要设计一个崩溃发生时的处理程序

```swift
var shouldKeepGoing = true
struct ExceptionHandler {
    func handleException(_ exception: NSException) {
        saveCriticalData()
        
        let alert = UIAlertController(title: "Oops", message: "something wrong", preferredStyle: .alert)
        let actionContinue = UIAlertAction(title: "Continue", style: .cancel) { (_) in
        }
        let actionCrash = UIAlertAction(title: "Let it crash", style: .destructive) { (_) in
            shouldKeepGoing = false
        }
        alert.addAction(actionContinue)
        alert.addAction(actionCrash)
        UIApplication.shared.keyWindow?.rootViewController?.present(alert, animated: true, completion: nil)
        
        let runloop = CFRunLoopGetCurrent()
        guard let allModesO = CFRunLoopCopyAllModes(runloop) as [AnyObject]? else { return }
        guard let allModes = allModesO as? [CFString] else { return }
        while shouldKeepGoing {
            for mode in allModes {
                CFRunLoopRunInMode(CFRunLoopMode(mode), 0.001, false)
            }
        }
    }
    
    private func saveCriticalData() {}
}

func setupUncaughtExceptionHandler() {
    NSSetUncaughtExceptionHandler { (exception) in
        ExceptionHandler().handleException(exception)
    }
}
```

- 这里，关键的，其实是调用系统提供的函数 `NSSetUncaughtExceptionHandler`
- 接着，先假定应用在发生异常时，用户还希望继续把 App 运行下去，设置全局变量 `shouldKeepGoing` 为 `true`, 接着，我们将通过弹窗的形式，让用户选择是否继续运行下去
- 由于应用发生了异常，因此，原来的 RunLoop 上下文出现问题，因此，抢救的措施就是给 RunLoop 起搏刺激，将 RunLoop 的每个 mode 都重新运行起来，在这里，就是最少运行 0.001s
- 由于部分 RunLoop Mode 是系统私有的，开发者并不能直接拿到。但通过 `CFRunLoopCopyAllModes(runloop)` 可以曲线获取到全部 mode
- 由于这个处理程序其实并没有返回，一直在循环，因此可知，其占用的栈空间或其他资源是无法释放的，这是一个 trade off

## References

- [Handling unhandled exceptions and signals](https://www.notion.so/Handling-unhandled-exceptions-and-signals-6f52fd6043a2448abb8241cefc3ea692#a43f67cb9624404081e14c8e87da413e)

