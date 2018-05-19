# Block

## Block 类型

名称 | 存储区域
--- | ---
_NSConcreteStackBlock | 栈
_NSConcreteGlobalBlock | 程序数据区域 .data
_NSConcreteMallocBlock | 堆

- 作为一个局部变量，Block 通常为 `_NSConcreteStackBlock`
- 作为一个全局变量，Block 通常为 `_NSConcreteGlobalBlock`
- 作为一个类的属性，Block 通常为 `_NSConcreteMallocBlock`

作为全局变量时，通常我们会将 Block 定义在函数外的地方，就成了全局变量，如

```objc
void (^blk)(void) = ^{};

int main() {
    return 0;
}
```

## 将 Block 从栈上复制到堆上

当我们需要在一个函数外使用到 Block 时，就需要将 Block 从栈上复制到堆上，避免当函数作用域结束后，位于栈上的 Block 失效的情况

### 自动复制

对于 ARC 而言，多数情况下，编译器会自动判断是否需要将 Block 从栈上复制到堆上，如，当一个函数返回一个 Block 时，编译器会自动将这个函数上的 Block 复制到堆上(编译器自动添加复制代码)

涉及到的函数有

```objc
objc_retainBlock
objc_autoreleaseReturnValue
_Block_copy
```

例子

```objc
typedef int (^blk_t)(int);

blk_t func(int rate) {
    return ^(int count){return rate * count;}; // 编译器添加代码，将这个 Block 复制到堆上
}
```

自动复制的情况

- Cocoa 框架的方法 && 方法中含有 `usingBlock`
    - `enumerateObjectsUsingBlock`
- GCD 的 API
    - `dispatch_async`

### 手动复制

手动复制，只需要在 Block 上调用 `copy` 方法

```objc
- (id)getBlockArray {
    return [[NSArray alloc] initWithObjects:
        ^{},
        ^{},
        nil,
    ];
}
```

上面的方法中，创建一个方法，这个方法返回一个数组，数组中包含了多个 Block. 然而，当执行这个方法后，在结果中取出某个 Block 时，就会发生异常出错

这是由于 Block 首先是会在函数的栈上生成，但在函数结束时，Block 没有从函数的栈上复制到堆上，被废弃了，导致从结果取出元素时发生异常

编译器在这种情况下，并不能判断是否需要将 Block 从栈上复制到堆上，因为将 Block 从栈到堆的复制操作是非常浪费 CPU 资源的。

此时，需要我们进行手动复制

```objc
- (id)getBlockArray {
    return [[NSArray alloc] initWithObjects:
        [^{} copy],
        [^{} copy],
        nil,
    ];
}
```

### 重复复制

对于已经在堆上的 Block, 进行重复的复制操作结果

Block 类型 | Block 原本的位置 | 结果
--- | --- | ---
_NSConcreteStackBlock | 栈 | 从栈复制到堆
_NSConcreteGlobalBlock | 程序数据区域 .data | 什么也不做
_NSConcreteMallocBlock | 堆 | 引用计数增加




