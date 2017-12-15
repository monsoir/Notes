# 实现对象的复制

设计两个协议

- `NSCopying`
- `NSMutableCopying`

`NSObject` 本身含有 `copy` 和 `mutableCopy` 的实例方法，但是并没有实现对应协议的方法。并且，这两个方法只是去调用对应协议的协议方法

若需要继承自 `NSObject` 的自定义类实现对象复制的功能，需要根据需要实现对应协议及其协议方法

## NSCopying

- 只有一个协议方法 `copyWithZone:`
- 调用对象的复制功能只需要简单地调用 `copy` 方法（继承自 `NSObject`）
- `copy` 方法内部自动调用 `copyWithZone:`
- 协议方法里面的 `zone` 不需要理会，不会再用了

```objc
/* 定义一个对象 */
@interface John: NSObject <NSCopying>

@property (nonatomic, copy) NSString *name;
@property (nonatomic, copy) NSString *introduction;

@end

@implementation John

- (instancetype)initWithName:(NSString *)name introduction:(NSString *)intro {
    self = [super init];
    
    if (self) {
        _name = name;
        _introduction = intro;
    }
    
    return self;
}

- (nonnull id)copyWithZone:(nullable NSZone *)zone {
    John *copyJohn = [[[self class] alloc] initWithName:self.name introduction:self.introduction];
    return copyJohn;
}

@end

/* 测试 */

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        John *j1 = [[John alloc] initWithName: @"John" introduction: @"version 1"];
        John *j2 = [j1 copy];
        
        NSLog(@"%@", j1); // <John: 0x10061c5b0>
        NSLog(@"%@", j2); // <John: 0x10061c6b0>
    }
    return 0;
}
```

可以看到，上面的 `j1` 和复制过来的 `j2` 地址已经不同了

## NSMutableCopying

### 浅复制

- 只有一个协议方法 `mutableCopyWithZone:`
- 调用对象的复制功能只需要简单调用 `mytableCopy` 方法（继承自 `NSObject`）
- `mutableCopy` 方法内部自动调用 `mutableCopyWithZone:`
- 协议方法中的 `zone` 不需要理会，不会再用了

```objc
John *t1 = [[John alloc] initWithName: @"John1" introduction: @"version 1"];
John *t2 = [[John alloc] initWithName: @"John2" introduction: @"version 2"];
John *t3 = [[John alloc] initWithName: @"John3" introduction: @"version 3"];

NSMutableArray *originArray = [NSMutableArray arrayWithArray:@[t1, t2, t3]];
NSMutableArray *mutableCopiedArray = [originArray mutableCopy];
NSMutableArray *immutableCopiedArray = [originArray copy];

/**
👆 结果

originArray: 41f840
content: (
    "<John: 0x10041eb70>",
    "<John: 0x10041eb90>",
    "<John: 0x10041ebb0>"
)

mutableCopiedArray: 41eb30
content: (
    "<John: 0x10041eb70>",
    "<John: 0x10041eb90>",
    "<John: 0x10041ebb0>"
)

immutableCopiedArray: 41fb30
content: (
    "<John: 0x10041eb70>",
    "<John: 0x10041eb90>",
    "<John: 0x10041ebb0>"
)
*/

NSLog(@"originArray: %@", [originArray class]);
NSLog(@"mutableCopiedArray: %@", [mutableCopiedArray class]);
NSLog(@"immutableCopiedArray: %@", [immutableCopiedArray class]);

/**
👆 结果
originArray: __NSArrayM
mutableCopiedArray: __NSArrayM
immutableCopiedArray: __NSArrayI
*/

John *t4 = [[John alloc] initWithName: @"John4" introduction: @"version 4"];
[originArray addObject:t4];
NSLog(@"originArray: %x\n content: %@", originArray, originArray);
NSLog(@"mutableCopiedArray: %x\n content: %@", mutableCopiedArray, mutableCopiedArray);
NSLog(@"immutableCopiedArray: %x\n content: %@", immutableCopiedArray, immutableCopiedArray);

/**
👆 结果
originArray: 41f840
content: (
    "<John: 0x10041eb70>",
    "<John: 0x10041eb90>",
    "<John: 0x10041ebb0>",
    "<John: 0x10041ff90>"
)

mutableCopiedArray: 41eb30
content: (
    "<John: 0x10041eb70>",
    "<John: 0x10041eb90>",
    "<John: 0x10041ebb0>"
)

immutableCopiedArray: 41fb30
content: (
    "<John: 0x10041eb70>",
    "<John: 0x10041eb90>",
    "<John: 0x10041ebb0>"
)
*/
```

从上面的结果可以看出

1. 通过 `mutableCopy` 出来的对象，仍然为 `mutable`
2. 通过 `copy` 出来的对象，仍然为 `immutable`
3. 无论是 `copy` 还是 `mutableCopy`, 影响的只是容器（在上面的例子指的是数组容器）的指针，即只复制出另一个数组容器，即原来的容器与新的容器已经是互相独立了，但是容器中的元素，并没有改变，可以看出元素的地址并没有发生变化
4. 像这种复制，叫做浅复制

### 深复制

像上面的例子，要实现深复制，即不仅实现容器的复制，还需要对元素进行复制，即原容器与新容器的地址不同，同时，原容器中的元素与新容器中对应的元素地址也要不同

1. 不断对数组中的元素进行遍历复制
2. 对数组进行归档，解档操作，即先将原数组转化为二进制数据，再从二进制数据中生成数组

