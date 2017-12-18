# Method Swizzling

可以实现方法的交换，例如，用自己实现的 `xxx_viewWillAppear:` 代替系统的 `viewWillAppear:`

## 涉及的概念

- Selector
- Method
- Implementation

### Selector(SEL)

- 在 Runtime 中，代表一个方法的名字
- 在 Runtime 中，是一个被映射成 C 类型字符串
- 由编译器产生，并当类被加载进内存时由 Runtime 进行映射

### Method

```objc
typedef struct objc_method *Method;
struct objc_method {
    SEL _Nonnull method_name                                 OBJC2_UNAVAILABLE;
    char * _Nullable method_types                            OBJC2_UNAVAILABLE;
    IMP _Nonnull method_imp                                  OBJC2_UNAVAILABLE;
} 
```

- 代表一个方法的定义

### Implementation(IMP)

- 指向一个方法实现的最开始地方

### 联系

- 在运行时，类维护一个消息列表来进行消息的发送
- 每个消息列表的入口是一个方法(Method)
- 一个 Method 维护着一对类似键值对(key-value)的东西，如上面 Method 的定义
    - key: 一个 SEL, 记录方法的名字
    - value: 一个 IMP, 记录方法具体实现的函数指针
- Method Swizzling 实际上就是把 Method 结构中的函数指针 IMP 重定向到另一个函数实现，并将原有的方法的实现重命名了一下

## 例子

### 例子模型

Thor

```objc
@interface Thor : NSObject

- (void)hammerFallStrike;

@end

@implementation Thor

- (void)hammerFallStrike {
    NSLog(@"%@ hammerFall strikes", self);
}

@end
```

### 运行

```objc
Thor *thor = [[Thor alloc] init];
        
NSLog(@"Thor hammerFall strikes:\n");
[thor hammerFallStrike];

// Thor hammerFall strikes:
// <Thor: 0x100608660> hammerFall strikes
```

### 添加 Method Swizzling Category

Thor+Swizzling

```objc
@interface Thor (Swizzling)
- (void)whipStrike;
@end


#include <objc/runtime.h>

@implementation Thor (Swizzling)

- (void)whipStrike {
    NSLog(@"%@, whip strikes", self);
}

+ (void)load {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        // 1. 获取 swizzle 前的方法名
        SEL originalSelector = @selector(hammerFallStrike);
        SEL swizzledSelector = @selector(whipStrike);

        Class class = [self class];
        // 2. 获取 swizzle 前的方法定义
        Method originalMethod = class_getInstanceMethod(class, originalSelector);
        Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);

        // 3. 尝试将 swizzledMethod 的实现添加到 originalSelector 的指向
        BOOL didAddMethod = class_addMethod(class, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod));
        if (didAddMethod) {
            // 若上面的尝试成功，那就将 originalMethod 的实现添加到 swizzledSelector 的指向，保证不丢失原有的方法
            class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));
        } else {
            // 若上面的尝试不成功，就是 originalSelector 中的方法实现已经有东西存在了
            // originSelector 的方法实现已经有东西了，那就直接将 originalSelector 和 swizzledSelector 的实现交换吧
            method_exchangeImplementations(originalMethod, swizzledMethod);
        }
    });
}

@end
```

### 再运行

```objc
Thor *thor = [[Thor alloc] init];
        
NSLog(@"Thor hammerFall strikes:\n");
[thor hammerFallStrike];

// Thor hammerFall strikes:
// <Thor: 0x100730980>, whip strikes
```

可以看到，但我们需要 Thor 使用 hammerFall 攻击的时候，TA 却耍起了 whip, 调用了同样的方法，得到了不同的结果

## 注意事项

### 什么时候要完成方法交换

⚠️ **应该只在 `+ (void)load` 中完成**

在运行时，每个类都有两个方法会自动调用，在方法有实现的情况下

- `+ (void)load` 类被初始装载时被调用
- `+ (void)initialize` 应用第一次调用类的类方法或实例方法前调用

### 方法交换保证只有一次的执行

⚠️ **应该只在 `dispatch_once` 中完成**

Method Swizzling 改变了全局的状态，采用单例的标准进行 Method Swizzling 保证了交换代码只执行一个，保证线程安全

### 需要警惕死循环的出现

```objc
- (void)xxx_viewWillAppear:(BOOL)animated {
    [self xxx_viewWillAppear:animated];
    NSLog(@"viewWillAppear: %@", NSStringFromClass([self class]));
}
```

- 上面的方法，如果没有与 `viewWillAppear:` 进行 Method Swizzling, 便会死循环
- 与 `viewWillAppear:` 进行了 Method Swizzling 后
    - `viewWillAppear:` 的实现指向了 `xxx_viewWillAppear:` 的实现
    - `xxx_viewWillAppear:` 的实现指向了 `viewWillAppear:` 的实现
- 换个话说，虽然方法名还是那个方法名，但
    - 当系统调用 `viewWillAppear:` 时，实际上执行的是 `xxx_viewWillAppear:` 的代码
    - 当调用 `xxx_viewWillAppear:` 时，实际上执行的是 `viewWillAppear:` 的代码

## References

- [NSHispter - Method Swizzling](http://nshipster.cn/method-swizzling/)

