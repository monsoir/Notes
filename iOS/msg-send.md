# 处理未知消息

向对象发送消息，如果没有在对象的模型上找到方法，系统会利用「消息转发」机制来动态处理这个问题

- 动态解析
- 消息转发
    - 只替换消息接收对象
    - 完整的消息转发

## 处理方式及对应涉及的方法

- 动态解析 (Dynamic Method Resolution)
    - `+(BOOL)resolveInstanceMethod:(SEL)sel;` 对应实例方法
    - `+(BOOL)resolveClassMethod:(SEL)sel;` 对应类方法

- 消息转发 (Message Forwarding)
    - `- (id)forwardingTargetForSelector:(SEL)aSelector`
    - `- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector`
    - `- (void)forwardInvocation:(NSInvocation *)anInvocation`

## 例子模型

1. `Human`, 继承自 `NSObject`
2. `Adam`, 继承自 `Human`
3. `Eve`, 继承自 `Human`
4. `Gain`, 继承自 `Human`

Human

```objc
@interface Human: NSObject
- (void)eat;
- (void)sleep;
@end

@implementation Human

- (void)eat {
    NSLog(@"%@ eats", self);
}

- (void)sleep {
    NSLog(@"%@ sleeps", self);
}

@end
```

Adam

```objc
@interface Adam: Human
- (void)work;
@end

@implementation Adam

- (void)work {
    NSLog(@"%@ works", self);
}

@end
```

Eve

```objc
@interface Eve: Human
- (void)birth;
@end

@implementation Eve

- (void)birth {
    NSLog(@"%@ births", self);
}
```

Gain

```objc
@interface Gain: Human
- (void)slay;
@end

@implementation Gain
- (void)slay {
    NSLog(@"%@ slayed Abel", self);
}
@end
```

## 动态解析

- `+(BOOL)resolveInstanceMethod:(SEL)sel;` 对应实例方法
- `+(BOOL)resolveClassMethod:(SEL)sel;` 对应类方法

动态解析，实际上还没有到达传说中的「消息转发」的地步，只是看能不能动态地给类添加方法，应用事例：使用 `@dynamic` 修饰的属性

### 参数

`sel` 未能处理的 selector

### 返回值

`BOOL` 是否添加了新方法来处理 selector

### 应用

```objc
Human *human = [[Human alloc] init];
[human eat];
[human sleep];
    
Adam *adam = [[Adam alloc] init];
[adam eat];
[adam sleep];
[adam work];

// 向 human 发送 work 消息，不进行方法动态解析的情况下，这会崩溃
[human performSelector:@selector(work)];
```

添加重定向的方法

```objc
void workSomething(id self, SEL _cmd) {
    NSLog(@"method resolved");
    NSLog(@"class: %@, selector: %@", self, NSStringFromSelector(_cmd));
//    NSLog(@"class: %@, selector: %s", self, sel_getName(_cmd)); // 与上一句效果一样
}
```

在 `Human` 上重写动态解析相关方法

```objc
#include <objc/runtime.h>

+ (BOOL)resolveInstanceMethod:(SEL)sel {
    NSString *selector = NSStringFromSelector(sel);
    if ([selector isEqualToString:@"work"]) {
        class_addMethod(self, sel, (IMP)workSomething, "v@:");
        return YES;
    }
    
    return [super resolveClassMethod:sel];
}
```

> 使用 Runtime 方法前，先引入这个文件
> 在这个例子中，class_addMethod 是 Runtime 的方法
> 
> class_addMethod 方法最后的参数解析
> 每个方法默认隐藏两个参数：self -> 调用者，_cmd -> 方法的 SEL
> `v@:`
> v -> 函数返回值为 void
> @ -> self
> : -> _cmd

## 消息转发

不实现方法的动态解析，也可以直接消息转发

消息转发也有两种实现

- 只替换消息接收对象
- 完整的消息转发````

### 只替换消息接收对象

`- (id)forwardingTargetForSelector:(SEL)aSelector`

#### 参数

`aSelector` 未能处理的 SEL

#### 返回值

`id` 重新接收消息的对象

#### 应用

```objc
Human *human = [[Human alloc] init];
[human eat];
[human sleep];

Eve *eve= [[Eve alloc] init];
[eve eat];
[eve sleep];
[eve birth];

// 向 human 发送 birth 消息，没有实现消息转发的情况下，这会崩溃
[human performSelector:@selector(birth)];
```

在 `Human` 中添加实现消息转发的方法

```objc
- (id)forwardingTargetForSelector:(SEL)aSelector {
    if ([Eve instancesRespondToSelector:aSelector]) {
        return [[Eve alloc] init];
    }
    
    return [super forwardingTargetForSelector:aSelector];
}
```

### 完整的消息转发

#### 相关的类与方法

- `NSInvocation`
    - 存储消息发送所需要的 target, selector, 参数等
- `NSMethodSignature`
    - 存储方法签名，包含方法的参数与返回值
- `- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel;`
    - 在消息转发时，用于创建 `NSInvocation`, 返回方法签名对象
- `- (void)forwardInvocation:(NSInvocation *)anInvocation;`
    - 在消息转发时，若对象没有消息的相关实现，Runtime 提供一次机会将消息委托给另一个接收者
- `- (void)invokeWithTarget:(id)anObject;`
- `- (void)doesNotRecognizeSelector:(SEL)aSelector;`
    - 到最后，消息转发还是没有解决问题，调用此方法抛出异常

#### 关于方法调用时机

在「只替换消息转发对象」的环节中，失败之后，Runtime 将会调用 `- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel;` 创建 `NSInvocation`. 然后调用 `- (void)forwardInvocation:(NSInvocation *)anInvocation;`, 在此方法中，会对新创建的 `NSInvocation` 调用 `- (void)invokeWithTarget:(id)anObject;` 方法，在这个方法中，会对新接收者(`anObject`)调用同名方法（那个未能处理的方法）

需要重写的方法

- `- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel;`
- `- (void)forwardInvocation:(NSInvocation *)anInvocation;`

需要手动调用的方法

- `- (void)invokeWithTarget:(id)anObject;`
- `- (void)doesNotRecognizeSelector:(SEL)aSelector;`


#### 应用

```objc
Human *human = [[Human alloc] init];
[human eat];
[human sleep];

Gain *gain = [[Gain alloc] init];
[gain eat];
[gain sleep];
[gain slay];

// 向 human 发送 work 消息，不进行消息转发的情况下，这会崩溃
[human performSelector:@selector(slay)];
```

在 `Human` 中实现消息转发的方法

```objc
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector {
    // 先看看父类有没有进行消息转发
    NSMethodSignature *signature = [super methodSignatureForSelector:aSelector];
    if (!signature) {
        if ([Gain instancesRespondToSelector:aSelector]) {
            signature = [Gain instanceMethodSignatureForSelector:aSelector];
        }
    }
    
    return signature;
}

- (void)forwardInvocation:(NSInvocation *)anInvocation {
    SEL sel = anInvocation.selector;
    if ([Gain instancesRespondToSelector:sel]) {
        Gain *gain = [[Gain alloc] init];
        [anInvocation invokeWithTarget:gain];
    } else {
        [self doesNotRecognizeSelector:sel];
    }
}
```

## References

- [Dynamic Method Resolution](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtDynamicResolution.html)
- [Message Forwarding](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html)
- [iOS消息转发机制与BlocksKit](http://blog.flight.dev.qunar.com/2016/12/29/BlockskitAndiOSMessage/)
- [轻松学习之 Objective-C消息转发](http://www.cocoachina.com/ios/20150604/12013.html)
- [iOS消息转发机制](http://www.jianshu.com/p/151edae1d6ee)

