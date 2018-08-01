# [RunLoop](http://blog.ibireme.com/2015/05/18/runloop/)

- [RunLoop概述](#RunLoop概述)
- [RunLoop与线程](#RunLoop与线程)
- [RunLoop对外的接口](#RunLoop对外的接口)
- [RunLoop的Mode](#RunLoop的Mode)
- [RunLoop内部逻辑](#RunLoop内部逻辑)
- [Apple使用RunLoop实现的功能](#Apple使用RunLoop实现的功能)

## RunLoop概述

- 广义的 Event Loop
	- 管理事件／消息
	- 让线程在没有处理消息时休眠避免资源占用、在有消息到来时立刻被唤醒
- 是一个对象
	- 提供一个入口函数
	- 进入“接受消息->等待->处理”的循环中
- iOS/OS X 系统中的
	- CFRunLoopRef
		- 位于 CoreFoundation 中
		- 提供纯 C 函数 API，所有这些的 API 都 **线程安全**
	- NSRunLoop
		- 基于 CFRunLoopRef 的封装
		- 提供面向对象的 API，这些 API **不是线程安全** 的

## RunLoop与线程

- 两种线程对象，一一对应
	- pthread_t
	- NSThread
- CFRunLoopRef 基于 pthread 管理
- Apple 不允许直接创建 RunLoop，但提供自动获取的函数
	- CFRunLoopGetMain()
	- CFRunLoopGetCurrent()
- 线程与 RunLoop 一一对应，其关系保存在一个全局的 Dictionary 中
	- 线程刚创建时 **没有** RunLoop，需要主动获取才会有
	- RunLoop 的创建发生在第一次获取时，销毁发生在线程结束
	- **只能** 在一个线程的内部获取其 RunLoop（主线程除外）

## RunLoop对外的接口

- CoreFoundation 中关于 RunLoop 的类
	- CFRunLoopRef
	- CFRunLoopModeRef -> 没有对外暴露
	- CFRunLoopSourceRef
	- CFRunLoopTimerRef
	- CFRunLoopObserverRef
- ![](http://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_0.png)
	- 一个 RunLoop 包含若干个 Mode，每个 Mode 又包含若干个 Source/Timer/Observer
	- 每次调用 RunLoop 主函数时，只能限定其中一个 Mode (`currentMode`)
	- 需要切换 Mode，只能退出 Loop，再进行 Mode 指定后进入
	- 需要分割开不同组的 Source/Timer/Observer，让其互不影响
	- Source/Timer/Observer 被统称为 **mode item**
		- 一个 item 可被同时加入多个  mode
		- 一个 item 被重复加入同一个 mode 不会有效果
		- 一个 mode 中一个 item 都没有，则 RunLoop 直接退出，不进入循环

- CFRunLoopSourceRef
	- 事件产生的地方
	- Source 的两个版本
		- Source0 & Source1
		- Source0
			- **只包含** 一个回调（函数指针）
			- **不能主动触发事件**，需要
				- `CFRunLoopSourceSignal(source)` 将 Source 标记为待处理
				- `CFRunLoopWakeUp(runloop)` 唤醒 RunLoop，让其处理事件
		- Source1
			- 包含 mach_port 和一个回调，用于通过内核和其它线程相互发送消息
			- 能主动唤醒 RunLoop 的线程

- CFRunLoopTimerRef
	- 基于时间的触发器
	- 和 NSTimer 是toll-free bridged 的，可以混用
	- 包含一个**时间长度**和一个回调
	- 加入 RunLoop 时，RunLoop 会注册对应的时间点，当时间点到，RunLoop 被唤醒并执行回调

- CFRunLoopObserverRef
	- 每个观察者都包含一个回调（函数指针)
	- RunLoop 状态发生变化时，观察者通过回调接受这个变化


## RunLoop的Mode

> struct __CFRunLoopMode {
    CFStringRef _name;            // Mode Name, 例如 @"kCFRunLoopDefaultMode"
    CFMutableSetRef _sources0;    // Set
    CFMutableSetRef _sources1;    // Set
    CFMutableArrayRef _observers; // Array
    CFMutableArrayRef _timers;    // Array
    ...
};
 
> struct __CFRunLoop {
    CFMutableSetRef _commonModes;     // Set
    CFMutableSetRef _commonModeItems; // Set<Source/Observer/Timer>
    CFRunLoopModeRef _currentMode;    // Current Runloop Mode
    CFMutableSetRef _modes;           // Set
    ...
};

- CommonModes 概念
	- 一个 Mode 可以将自己标记为 Common 属性，通过将其 ModeName 添加到 RunLoop 的 "commonModes" 中
	- 每当 RunLoop 内容发生变化时，RunLoop 会自动将 _commonModeItems 里的 Source/Observer/Timer 同步到具有 "Common" 标记的所有Mode里
- Apple 公开提供的 Mode，都被标记为 `Common` 属性
	- kCFRunLoopDefaultMode(NSDefaultRunLoopMode)
	- UITrackingRunLoopMode

## RunLoop内部逻辑

![](http://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_1.png)

## Apple使用RunLoop实现的功能

- 系统默认注册的5个Mode
	1. kCFRunLoopDefaultMode -> App 的默认 Mode，主线程运行在这个 Mode 中
	2. UITrackingRunLoopMode -> 界面跟踪 Mode, 用于 Scroll view 追踪触摸滑动，保证界面滑动时不受其他 Mode 影响
	3. UIInitializationRunLoopMode -> App 刚启动进入的第一个 Mode，启动完成后不再使用
	4. GSEventReceiveRunLoopMode -> 接受系统事件的内部 Mode
	5. kCFRunLoopCommonModes -> 占位 Mode，没有实际作用

- AutoreleasePool
	- App启动后，苹果在主线程 RunLoop 里注册了两个 Observer
		1. 监听 Entry（即将进入 Loop)，创建自动释放池
		2. 监听 BeforeWaiting（准备进入休眠），释放旧池并创建新池；监听 Exit（即将退出 RunLoop），释放自动释放池

- 事件响应
	- 注册了一个 Source1 来接收系统事件，这里为硬件事件（触摸/锁屏/摇晃等）

- 手势识别
	- 监听 BeforeWaiting（准备进入休眠）
	- 当 UIGestureRecognizer 状态发生改变，都产生回调

- 界面更新
	- 操作 UI 时
		- 改变 Frame
		- 更新 UIView/CALayer 层次
		- 手动调用 `setNeedsLayout/setNeedsDisplay`
	- 监听 BeforeWaiting（准备进入休眠）和 Exit (即将退出Loop)

- 定时器
	- 就是 CFRunLoopTimerRef
	- RunLoop为了节省资源，并不会在非常准确的时间点回调这个Timer
	- Timer 有个属性叫做 Tolerance (宽容度)，标示了当时间点到后，容许有多少最大误差

- PerformSelecter
	- `performSelecter:afterDelay:` 
		- 内部会创建一个 Timer 并添加到当前线程的 RunLoop 中。所以如果当前线程没有 RunLoop，则这个方法会失效
	- `performSelector:onThread:`
		- 会创建一个 Timer 加到对应的线程去，同样的，如果对应线程没有 RunLoop 该方法也会失效

- About 网络请求
	- 网络请求接口自下而上的几层
		1. CFSocket
			- 最底层，只负责 socket 通信
		2. CFNetwork
			- 基于 CFSocket 等接口的上层封装
		3. NSURLConnection
			- 基于 CFNetwork 的更高层的封装，提供面向对象的接口
		4. NSURLSession
			- 表面上是和 NSURLConnection 并列的，但底层仍然用到了 NSURLConnection 的部分功能
	- ![](http://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_network.png)
		- 开始网络传输时，NSURLConnection 创建两个新线程
			- com.apple.CFSocket.private
			- com.apple.NSURLConnectionLoader

