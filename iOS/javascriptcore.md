# JavaScriptCore

- JavaScriptCore 主要的类及之间的关系
- Native 调用 JavaScript
- JavaScript 调用 Native
    - JavaScript 方法注册，Native 调用
    - 使用 `JSExport` 协议

配套一个例子：[JavaScriptCoreBox](https://github.com/Monsoir/JavaScriptCoreBox)

## JavaScriptCore 主要的类及之间的关系

JavaScriptCore 中主要的类有

- JSVirtualMachine
    - JavaScript 代码都在一个虚拟机中运行，而这个虚拟机就是 `JSVirtualMachine` 的实例
    - 一般情况下不会直接操作这个类
    - 当需要支持并发的 JavaScript 时，才会需要多个 `JSVirtualMachine` 的实例，因为 JavaScript 本身是单线程的
    - 每个 `JSVirtualMachine` 实例都会有自己的堆和垃圾回收器，因此，多个 `JSVirtualMachine` 实例间并不能传递信息
    - 默认会自动创建一个 `JSVirtualMachine` 实例来运行 JavaScript 代码

- JSContext
    - 一个 `JSContext` 是一个 JavaScript 执行的上下文
    - 对应到 Web 页面，它就是一个 `window` 对象
    - 可以创建多个 `JSContext` 对象
    - 多个 `JSContext` 对象之间可以互相传递信息

- JSValue
    - Native 与 JavaScript 通信的唯一的数据类型
    - 从 `JSContext` 出来到 Native 的数据，它们的类型都是 `JSValue`

- JSManagedValue
    - 对 `JSValue` 的封装
    - 用于自动管理 `JSValue` 的内存
    - 主要使用于：在 Native 对象中存储 JavaScript 对象

- `JSExport` 协议
    - 用于将 native 的类及其方法、属性，导出到 JavaScript 中时可用

> `JSVirtualMachine`, `JSContext`, `JSValue` 三者的关系

![](https://koenig-media.raywenderlich.com/uploads/2016/02/javascriptcore.png)

## Native 调用 JavaScript 代码

1. 创建 `JSContext` 实例
2. 运行 `[context evaluateScript:jscode]` 来直接运行 JavaScript 代码或注册 JavaScript 代码
3. 若在 2 中注册了 JavaScript 代码，如，注册了一个方法 `foo`, 则可以通过 

    ```objc
    JSValue *fooFunction = [self.context objectForKeyedSubscript:@"foo"];
    // 或者，两种方法的效果一样，但在 Swift 中只可以使用 👆
    JSValue *fooFunction = self.context[@"foo"];
    ```
    
    来获取到 `foo` 方法，从 JavaScript 返回来的值都是 `JSValue` 类型

4. 获取到 JavaScript 方法后，可以直接调用

    ```objc
    NSArray *result = [[fooFunction callWithArguments:@[@(TheArgument)]] toArray];
    ```
    
    - 在 `callWithArguments` 中，如果没有参数传递，则直接传 `nil`
    - 由于 JavaScript 返回来的数据都是 `JSValue` 类型，要转为 Native 对象，则需要调用类似 `toArray` 的方法来进行转换


## JavaScript 调用 Native 代码

### JavaScript 方法注册，Native 调用

```objc
// 注册
self.context[@"outterLog"] = ^(NSString *logMsg) {
    return [NSString stringWithFormat:@"outterLog: %@", logMsg];
};

// 调用
NSString *result = [[self.context evaluateScript:@"outterLog('hey, log from outter')"] toString];
self.tvReturnValue.text = [NSString stringWithFormat:@"innerLog: %@", result];

// 注册
// 注意  retain cycle
__weak typeof(self) weakSelf = self;
self.context[@"innerLog"] = ^(NSString *logMsg) {
    dispatch_async(dispatch_get_main_queue(), ^{
        weakSelf.tvWithoutReturnValue.text = [NSString stringWithFormat:@"innerLog: %@", logMsg];
    });
};

// 调用
[self.context evaluateScript:@"innerLog('hey, log from inner')"];
```

- 通过键值赋值的方法，将 Objective-C 的 Block(JavaScript 方法)，值传入
- 如果想要在 Block 中获取到关于执行上下文(`JSContext`)等信息，可以使用 `JSContext` 本身提供的类方法，这样就不用 weak-strong dancing 了
    - `currentContext`
    - `currentCallee`
    - `currentThis`
    - `currentArguments`
    - ...

### 使用 `JSExport` 协议

1. 自定义一个协议，继承于 `JSExport` 协议，如

    ```objc
    @protocol HumanJSable<JSExport>
    
    @property (nonatomic, copy) NSString *name;
    - (NSString *)nameIt;
    
    @end
    ```
    
2. 新建 Native 类，该类实现自定义的协议
3. 新建 Native 对象，并将对象以键值方式赋值，传递到 `JSContext` 中
4. 通过 JavaScript 代码直接调用

    ```objc
    Human *human = [[Human alloc] initWithName:@"Alice" age:20];
    self.context[@"human"] = human;
        
    NSString *name = [[self.context evaluateScript:@"human.nameIt()"] toString];
    self.tvResult.text = name;
    ```

#### Objective-C 对象导出到 JavaScript

- 在 JavaScript 中，继承是通过原型链来实现
- 对于每一个导出的 Objective-C 类，JavaScript 会在一个 `JSContext` 对象中创建一个原型
- 对于 `NSObject` 类，原型对象就是 `JSContext` 的 `Object` 原型
- 对于其他 Objective-C 类，JavaScriptCore 也会创建一个原型对象，而这个原型对象的私有属性 `[Prototype]` 指向 Objective-C 类的父类

#### Objective-C 方法属性暴露到 JavaScript

- 默认情况下，Objective-C 类是不会暴露任何方法或属性到 JavaScript
- 要将 Objective-C 类的方法暴露到 JavaScript, 需要将相应的方法和属性添加到继承自 `JSExport` 协议的自定义协议中，这样，JavaScriptCore 将该协议中的方法和属性解释成一个表
- 对于 Objective-C 类的实例方法的导出，JavaScriptCore 会为相应的原型对象创建一个函数属性
- 对于 Objective-C 类的属性的导出，JavaScriptCore 会为相应的原型对象创建一个属性访问器
- 对于 Objective-C 的类方法的导出，JavaScriptCore 会在类的创建函数对象上创建一个函数

## References

- [JavaScriptCore](https://developer.apple.com/documentation/javascriptcore)
- [JavaScriptCore Tutorial for iOS: Getting Started](https://www.raywenderlich.com/124075/javascriptcore-tutorial)
- [iOS JavaScriptCore使用](http://liuyanwei.jumppo.com/2016/04/03/iOS-JavaScriptCore.html)

