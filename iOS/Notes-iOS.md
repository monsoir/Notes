# [RunLoop](http://blog.ibireme.com/2015/05/18/runloop/)

- 广义的 Event Loop
	- 管理事件／消息
	- 让线程在没有处理消息时休眠避免资源占用、在有消息到来时立刻被唤醒
- 是一个对象
	- 提供一个入口函数
	- 进入“接受消息->等待->处理”的循环中
- iOS/OS X 系统中的
	- CFRunLoopRef
		- 位于 CoreFoundation 中
		- 提供纯 C 函数 API，所有这些的 API 都 ==线程安全==
	- NSRunLoop
		- 基于 CFRunLoopRef 的封装
		- 提供面向对象的 API，这些 API ==不是线程安全== 的

### RunLoop 与 线程

- 两种线程对象，一一对应
	- pthread_t
	- NSThread
- CFRunLoopRef 基于 pthread 管理
- Apple 不允许直接创建 RunLoop，但提供自动获取的函数
	- CFRunLoopGetMain()
	- CFRunLoopGetCurrent()
- 线程与 RunLoop 一一对应，其关系保存在一个全局的 Dictionary 中
	- 线程刚创建时 ==没有== RunLoop，需要主动获取才会有
	- RunLoop 的创建发生在第一次获取时，销毁发生在线程结束
	- ==只能== 在一个线程的内部获取其 RunLoop（主线程除外）

### RunLoop 对外的接口

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
	- Source/Timer/Observer 被统称为 ==mode item==
		- 一个 item 可被同时加入多个  mode
		- 一个 item 被重复加入同一个 mode 不会有效果
		- 一个 mode 中一个 item 都没有，则 RunLoop 直接退出，不进入循环

- CFRunLoopSourceRef
	- 事件产生的地方
	- Source 的两个版本
		- Source0 & Source1
		- Source0
			- ==只包含== 一个回调（函数指针）
			- ==不能主动触发事件==，需要
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


### RunLoop 的 Mode

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

### RunLoop 内部逻辑

![](http://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_1.png)

### Apple 使用 RunLoop 实现的功能

- 系统默认注册的5个Mode
	1. kCFRunLoopDefaultMode -> App 的默认 Mode，主线程运行在这个 Mode 中
	2. UITrackingRunLoopMode -> 界面跟踪 Mode, 用于 Scroll view 追踪触摸滑动，保证界面滑动时不受其他 Mode 影响
	3. UIInitializationRunLoopMode -> App 刚启动进入的第一个 Mode，启动完成后不再使用
	4. GSEventReceiveRunLoopMode -> 接受系统事件的内部 Mode
	5. kCFRunLoopCommonModes -> 占位 Mode，没有实际作用

- AutoreleasePool
	- App启动后，苹果在主线程 RunLoop 里注册了两个 Observer
		1. ==监听== Entry（即将进入 Loop)，创建自动释放池
		2. ==监听== BeforeWaiting（准备进入休眠），释放旧池并创建新池；==监听== Exit（即将退出 RunLoop），释放自动释放池

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


# 关联对象

在 Category 中添加属性

涉及的方法：

- `void objc_setAssociatedObject(id object, void *key, id value, objc_AssociationPolicy policy)`
	- 相当于 getter
- `id objc_getAssociatedObject(id object, void *key)`
	- 相当于 setter
- `void objc_removeAssociatedObjects(id object)`
	- 重设值，不直接调用，对新增的属性置 nil 即可

需引入 `#import <objc/runtime.h>`

NSObject+AssociatiedObject.h

```objc
@interface NSObject (AssociatedObject)
@property (nonatomatic, strong) id associatedObject;
@end
```

NSObject+AssociatedObject.m

```objc
@implementation NSObject (AssociatedObject)
@dynamic associatedObject; // 告诉编译器不需要生成默认的 setter 和 getter，runtime 时实现

//static char associatedObjectKey; //其实最好用这个东西来表明地址

- (void)setAssociatedObject:(id)object{
		objc_setAssociatedObject(self, @selector(associatedObject), associatedObject, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
		
		//objc_setAssociatedObject(self, &associatedObjectKey, associatedObject, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (id)associatedObject{
		return objc_getAssociatedObject(self, @selector(associatedObject));
		
		//return objc_getAssociatedObject(self, &associatedObjectKey);
}
@end
```

# Use `Singleton` Correctly

- 创建单例

	```objc
	static ShareInstanceClass *instance = nil;
+ (instancetype)sharedInstance
{
    	static dispatch_once_t onceToken;
    	dispatch_once(&onceToken, ^{
        	instance = [[[self class] alloc] init];
    	});
    	return instance;
}
	```
	
	- 仅此创建，在使用 `[[ShareInstanceClass alloc] init]`、`[ShareInstanceClass new]` 时，也是会创建不同的实例

- 重写 `allocWithZone:`

	```objc
	+ (instancetype)allocWithZone:(struct _NSZone *)zone
{
    	static dispatch_once_t onceToken;
    	dispatch_once(&onceToken, ^{
        	instance = [super allocWithZone:zone];
    	});
    	return instance;
}
	```
	- 单例的其他 property 一般会在 `init` 方法中初始化，若使用者直接使用了 `init` 方法，`ShareInstanceClass` 指针确实不便，但 `ShareInstanceClass` 的 property 指针会发生改变

- 将 property 初始化或默认值防止到 `dispatch_once` 的 block 中进行

	```objc
	static ShareInstanceClass *instance = nil;
+ (instancetype)sharedInstance
{
    	static dispatch_once_t onceToken;
    	dispatch_once(&onceToken, ^{
        	instance = [[[self class] alloc] init];
        	
        	/* Initialize the property of the ShareInstanceClass */
        	
    	});
    	return instance;
}
	```

- 较为健壮的创建代码

	```objc
static ShareInstanceClass *instance = nil;
+ (instancetype)sharedInstance
{
		static dispatch_once_t onceToken;
    	dispatch_once(&onceToken, ^{
        	instance = [[[self class] alloc] init];
        	
        	/* Initialize the property of the ShareInstanceClass */
        	
    	});
    	return instance;
}

	+ (instancetype)allocWithZone:(struct _NSZone *)zone
{
    	static dispatch_once_t onceToken;
    	dispatch_once(&onceToken, ^{
        	instance = [super allocWithZone:zone];
    	});
    	return instance;
}

	```

# 多线程
[Link](http://www.infoq.com/cn/articles/os-x-ios-multithread-technology)

### 使用 `performSelectors` 前缀的方法

### 使用 NSThread
- 轻量级任务
- 需要自己管理线程声明周期，进行线程间同步：线程状态，依赖性，线程间同步
- 线程间同步需用到：`NSLock`, `NSCondition`, `@synchronized`

### 使用 NSOperation
- 做的事情比 NSThread 多
- 通过继承 `NSOperation`，可以使子类获得一些线程相关的特性，进而安全地管理线程的生命周期
	- 以线程安全的状态建立状态
	- 取消线程（**独有**）
- 配合 `NSOperationQueue`，控制线程间的优先级和依赖性
- 一个 `NSOperationQueue` 代表一个独立的计算单元或任务
- 可以直接使用的实体子类
	- `NSInvocationOperation` 计算任务封装在方法中
	- `NSBlockOperation` 计算任务封装在 Block 中
- 不处理线程间的同步问题，因此同步线程需要 **同步锁**
- 使用状态机模型表示状态，可以使用 KVO 观察任务执行状态
- 可以设置 Operation 间的依赖性

### NSOperationQueue
- 用于执行计算任务，管理计算任务的优先级，处理计算任务间的依赖性
- NSOperation 被添加到 NSOperationQueue 后，队列按照 **优先级** 和 **进入顺序** 调度任务，NSOperation 自动执行

### 使用 GCD
- 在多核系统上高效运行并发代码，必用考虑繁琐的底层问题
- Block 是 GCD 的执行单元
- GCD 创建，重用，销毁线程，基于系统资源以它认为合适的方式运行每个队列

### GCD 队列
1. 串行分发队列 (Serial dispatch queue)
	- 私有分发队列 (private concurrent queue)
	- 按顺序执行队列中的任务
	- 同一时间只执行一个任务
	- 常用于实现 **同步锁**
	- `dispatch_queue_t serialQueue = dispatch_queue_create("com.example.MyQueue", NULL);`
2. 并发分发队列 (Concurrent dispatch queue)
	- 全局分发队列
	- 按顺序执行队列中的任务（同 串行分发队列），但
	- 顺序开始的多个任务会 **并发同时执行**
	- 常用于 **管理并发任务**
	- `dispatch_queue_t concurrentQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);`
3. 主分发队列 (Main dispatch queue)
	- 特殊串行分发队列，全局唯一
	- 主线程执行，用于执行 UI 相关操作
	- `dispatch_queue_t mainQueue = dispatch_get_main_queue();`
	- 其他主题
		- 分发组 dispatch group
		- 信号 semaphores
		- 分发栅栏 dispatch barrier
		- 分发源 dispatch source
	- 建议使用 GCD
		- 使用 dispatch queue 实现同步锁
		- 替代 `performSelector` 系列方法
		- 使用 `dispatch_once`　实现线程安全单一执行要求：单例

### 同步锁的实现
1. 使用属性的 `atomic` 属性
2. 使用 `@synchronized` 块
3. 使用 `NSLock`，极端状况下，有死锁的危险

```objc
// 使用 @synchronized 实现同步锁
- (void)methodNeedsToSynchronize:(id)identifier {
		/* Unique identifer 用于识别不同的同步锁，涉及同一个属性的不同线程应该使用同一个 identifier，一般使用 self，但涉及多个属性是，应使用不同的 identifier*/
		@synchronized(identifier/* Unique identifier*/) {
			// safe
		}
}

// 使用 NSLock 实现同步锁
_lock = [[NSLock alloc] init];
- (void)methodNeedsToSynchronize {
		[_lock lock];
		
		// safe
		
		[_lock unlock];
}
```

### NSOperation > GCD
1. 需要取消任务
2. 需要更详细地观察任务的状态，NSOperation 使用 KVO 进行观察
3. 需要重用线程任务，NSOperation 作为 Objective-C 对象，能存储更多的信息


### 串行分发队列 与 并行分发队列
- 生成过多队列，导致生成过多线程，导致大量内存被消耗，不可“贪杯”

##### 串行分发队列 (Serial Dispatch Queue)
- 建议只在 **多个线程更新相同资源导致数据竞争** 时使用
- `dispatch_queue_t serialDispatchQueue = dispatch_queue_create("com.example.gcd.SerialDispatchQueue", NULL);`
	- 生成串行队列时，将第二个参数指定为 `NULL`

##### 并行分发队列 (Concurrent Dispatch Queue)
- 并行执行不发生数据竞争等问题时使用
- `dispatch_queue_t concurrentDispatchQueue = dispatch_queue_create("com.example.gcd.ConcurrentDispatchQueue", DISPATCH_QUEUE_CONCURRENT);`
	- 生成并行分发队列时，将第二个参数指定为 `DISPATCH_QUEUE_CONCURRENT`

##### 线程内存管理 
- 尽管有 ARC 的存在，但生成的 Dispatch Queue 需要由开发者负责释放
- 持有 与 释放 的函数
	- `dispatch_release(dispatchQueue)`
	- `dispatch_retain(dispatchQueue)`

```objc
// 生成一个队列
dispatch_queue_t concurrentDispatchQueue = dispatch_queue_create("com.example.gcd.ConcurrentDispatchQueue", DISPATCH_QUEUE_CONCURRENT);

// 将任务通过 Block 分配给队列
dispatch_async(queue, ^{
	// Do something.
});

// 释放
dispatch_release(concurrentDispatchQueue);

/*
任务通过 dispatch_async 指派到队列后，立刻调用 dispatch_release 函数是正确的
1. dispatch_async 函数中追加 Block 到 concurrentDispatchQueue 中，
   Block 通过 dispatch_retain 函数持有 concurrentDispatchQueue
2. 随后立即调用 dispatch_release，由于 concurrentDispatchQueue 被 Block 持有，
   concurrentDispatchQueue 不会被释放
3. 当 Block 执行完毕后，释放 concurrentDispatchQueue，此时 concurrentDispatchQueue 被回收
*/
```

- 在 GCD 的 API 中，含有 `create` 的 API，需要在必要时通过 `dispatch_release` 进行释放
- 通过函数或方法获取的 Dispatch Queue，有必要通过 `dispatch_retain` 进行持有，通过 `dispatch_release` 进行释放

### 改变生成队列的优先级 dispatch_set_target_queue
- 使用 `dispatch_queue_create` 生成的 Dispatch Queue 不管是串行还是并行，其优先级都为默认优先级
- 通过 `dispatch_set_target_queue` 函数改变优先级

```objc
// 创建自己的队列
dispatch_queue_t serialDispatchQueue = dispatch_queue_create("com.example.gcd.SerialDispatchQueue", NULL);

// 获取到系统提供队列，并且其优先级为目标优先级
dispatch_queue_t globalDispatchQueueBackground = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0);

// 设置优先级
dispatch_set_target_queue(serialDispatchQueue, globalDispatchQueueBackground);
```

- 第一个参数：**需要变更优先级** 的队列
- 第二个参数：**拥有目标优先级** 的队列

### 延迟执行
- `dispatch_after`

```objc
// 设定时间
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 3ull * NSEC_PER_SEC);

// 执行
dispatch_after(time, dispatch_get_main_queue(), ^{
	// Do something.
});
```

- 然而，`dispatch_after` 并不是在指定的时间执行处理，而是，在指定时间追加任务处理到 Dispatch Queue，即在指定时间后采取排队。因此，一般执行的时间会 **有延迟**

#### dispatch_time_t 类型
- 使用 `dispatch_time` 或 `dispatch_walltime` 函数生成

##### dispatch_time

```objc
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 3ull * NSEC_PER_SEC);
```

- 数值与 `NSEC_PER_SEC` 的乘积得到**单位为毫微秒**的数值
	- `#define NSEC_PER_SEC 1000000000ull`
- `ull` 为 C语言的数值字面量，表示 `unsigned long long`
- `NSEC_PER_MSEC` 为毫秒
- 常用于计算 **相对时间**

##### dispatch_walltime

```objc
dispatch_time_t dispatchTimeByDate(NSDate *date) {
	NSTimeInterval interval;
	double second, subsecond;
	struct timespec time;
	dispatch_time_t milestone;
	
	interval = [date timeIntervalSince1970];
	
	// 将小数分拆为整数与小数部分, interval 为整数部分，
	// &second 为指向存储小数部分位置的地址
	subsecond = modf(interval, &second);
	time.tv_sec = second;
	time.tv_nsec = subsecond * NSEC_PER_SEC;
	milestone = dispatch_walltime(&time, 0);
	
	return milestone;
}
```

- 通过 `struct timespec` 类型的时间得到 `dispatch_time_t` 类型的值
- 通常用于计算绝对时间


### 派遣组，同步多个队列 Dispatch Group

>在 Dispatch Queue 中处理多个任务后最后执行结束操作，只需要使用一个 Serial Dispatch Queue，并将所有任务追加到该 Dispatch Queue 中。
>但在使用 Concurrent Dispatch Queue 时，或同时使用多个 Dispatch Queue 时，我们应该使用 Dispatch Group。

#### dispatch_group_async

```objc
// 1. 获得一个 并发队列
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

// 2. 创建一个派遣组
dispatch_group_t group = dispatch_group_create();

// 3. 对 group 进行监视，并将任务添加到 queue 中
dispatch_group_async(group, queue, ^{/*block 1*/});
dispatch_group_async(group, queue, ^{/*block 2*/});
dispatch_group_async(group, queue, ^{/*block 3*/});

// 4. 添加任务完成之后的处理，即最后一步
dispatch_group_notify(group, dispatch_get_main_queue(), ^{/*Final task*/});

// 5. 同样，dispatch group 也需要被释放，ARC 不负责释放 Dispatch Queue 和 Dispatch Group
dispatch_release(group);
```

- 与 `dispatch_async` 一样，将任务追加到指定的 Dispatch Queue 中
- 与 Dispatch Queue 一样，Block 通过 `dispatch_retain` 持有 Dispatch Group，由于 **ARC 不负责释放 Dispatch Queue 和 Dispatch Group**，因此任务派遣完毕后，需要手动释放

#### dispatch_group_wait

```objc
// 1. 创建一个队列
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

// 2. 创建一个派遣组
dispatch_group_t group = dispatch_group_create();

// 3. 对 group 进行监视，并将任务添加到 queue 中
dispatch_group_async(group, queue, ^{/*block 1*/});
dispatch_group_async(group, queue, ^{/*block 2*/});
dispatch_group_async(group, queue, ^{/*block 3*/});

// 4. 等待所有任务结束
dispatch_group_wait(group, DISPATCH_TIME_FOREVER);

// 5. 同样，dispatch group 也需要被释放，ARC 不负责释放 Dispatch Queue 和 Dispatch Group
dispatch_release(group);
```

- dispatch_group_wait
	- 参数2: 等待的时间（超时），为 `disaptch_time_t` 类型，也参照上文来生成 `dispatch_time_t` 类型参数
	- 返回结果
		- 不为0: 经过了指定时间（返回的结果），但属于 Diapatch Group 的某一处理仍在进行中\
		- 为0: 全部处理执行结束
	- `DISPATCH_TIME_FOREVER` 超时时间等于此值时，`dispatch_group_wait` 恒返回0
	- `DISPATCH_TIME_NOW` 不用等待即可判定 Dispatch Group 的处理是否执行结束
	- 等待 wait 意味着
		- 一旦调用 `dispatch_group_wait`，函数处于调用的状态而不返回，执行 `dispatch_group_wait` 函数的当前线程，在经过超时时间或 Dispatch Group 中的任务结束之前，都会停止

### 解决并发队列中数据竞争的问题

```objc
dispatch_async(queue, readingBlock1);
dispatch_async(queue, readingBlock2);
dispatch_async(queue, readingBlock3);
dispatch_async(queue, readingBlock4);

dispatch_barrier_async(queue, writingBlock);

dispatch_async(queue, readingBlock5);
dispatch_async(queue, readingBlock6);
dispatch_async(queue, readingBlock7);
dispatch_async(queue, readingBlock8);
```

#### dispatch_barrier_async
1. `dispatch_barrier_async` 函数会等待追加到并发队列上的并行执行的任务全部结束（如上面代码的  readingBlock1~4 ）
2. 将指定的处理（如上面代码 writingBlock ）追加到并发队列上
3. 并发队列等待 2. 中的任务结束后，再恢复为一般的操作（继续并发执行，如继续执行上面代码的 readingBlock5~8 ）

因此，执行通过 `dispatch_barrier_async` 添加的任务，会暂停并发队列的并发特性，只执行改任务。

