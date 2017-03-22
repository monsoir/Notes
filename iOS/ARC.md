[TOC]

# ARC

## 属性声明与所有权修饰符的对应关系

属性声明 | 所有权修饰符
---|---
assign | __unsafe_unretained
copy | __strong 对象是被复制过来的
retain | __strong
strong | __strong
unsafe_unretained | __unsafe__unretained
weak | __weak

## ARC 的实现

### __strong

#### 关键函数
- `objc_msgSend` 
	- 给对象发送消息
- `objc_release` 
	- 释放对象
- `objc_autoreleaseReturnValue`
	- 返回注册到 autoreleasepool 中的对象
	- 用于 `alloc/new/copy/mutableCopy` 的实例创建方法中
	- 顾名思义：等我不用这个对象的时候，自动释放吧
- `objc_retainAutoreleasedReturnValue` 
	- 自己持有对象，但持有的对象是为注册在 autoreleasepool 中的对象
	- 用于像 `alloc/new/copy/mutableCopy` **之外** 的一些实例创建方法 
	- 顾名思义：retain 住那个会 autorelease 的对象，因为这个对象在之前的方法就已经在另一个方法中定义为 autorelease

#### 实例

源代码 1

```objc
{
	id __strong obj = [[NSObject alloc] init];
}
```

```objc
// 编译器模拟代码
id obj = objc_msgSend(NSObject, @selector(alloc));
objc_msgSend(obj, @selector(init));
objc_release(obj); // 变量作用域结束时释放对象
```

源代码 2 - objc_retainAutoreleasedReturnValue

```objc
{
	id __strong obj = [NSMutableArray array];
}
```

```objc
// 编译器模拟代码
id obj = objc_msgSend(NSMutableArray, @selector(array));
objc_retainAutoreleasedReturnValue(obj);
objc_release(obj);
```

源代码 3 - objc_autoreleaseReturnValue

```objc
+ (id)array {
	return [[NSMutableArray alloc] init];
}
```

```objc
// 编译器模拟代码
+ (id)array {
	id obj = objc_msgSend(NSMutableArray, @selector(alloc));
	objc_msgSend(obj, @selector(init));
	return objc_autoreleaseReturnValue(obj);
}
```

### __weak

#### 关键函数

- objc_initWeak
- objc_destroyWeak
- objc_storeWeak
	- 将 2nd 参数赋值对象的地址作为键(key)，将 __weak 修饰的 1st 参数的地址注册到 weak 表中
	- 若 2nd 参数为 0，则将变量的地址从 weak 表中移除

#### weak 表

- 使用 **散列表** 实现，与 计数引用表相同
- 根据 objc_storeWeak 的函数叙述


	key | value
	--- | ---
	实际上的对象的地址 | __weak 修饰的变量的地址

- 当 key 为 0 时，通过散列表的特性，可以快速获取到所有 __weak 对象的地址，并全部置 nil
	- 如何快速地获取到所有 __weak 对象地址？可看作，当 hash 到同一个 key 的时候，把 value 用一个链表进行管理，因此置 nil 的时候，只需要把链表中的全部对象置 nil

#### 对象释放的过程

1. `objc_release`
2. 引用计数为 0 -> 调用 `dealloc`
3. `_objc_rootDealloc`
4. `object_dispose`
5. `objc_destructInstance`
6. `objc_clear_deallocating` <- Magic Happens Here
	1. 从 weak 表中以该对象注册的所有 weak 记录
	2. 将 weak 记录中所有变量置为 nil
	3. 从 weak 表中删除对象
	4. 从引用计数表中删除以该对象地址为键值的记录

#### 实例

源代码 1

```objc
{
	id __weak obj1 = obj;
}
```

```objc
// 编译器模拟代码
id obj1;
objc_initWeak(&obj1, obj);
objc_destroyWeak(&obj1);
```

```objc
// 将上面的代码再分拆

// objc_initWeak(&obj1, obj);
id obj1 = 0;
objc_storeWeak(&obj1, obj);

// objc_destroyWeak(&obj1);
objc_storeWeak(&obj1, 0);
```


### __autoreleasing

#### 关键函数

- objc_autoreleasePoolPush()
- objc_autorelease
- objc_retainAutoreleasedReturnValue
- objc_autoreleasePoolPop

#### 实例

重点都是有 objc_autorelease

源代码 1 - alloc - init

```objc
@autoreleasepool {
	id __autorelease obj = [[NSObject alloc] init];
}
```

```objc
// 编译器模拟代码
id pool = objc_autoreleasePoolPush();
id obj = objc_msgSend(NSObject, @selector(alloc));
objc_msgSend(obj, @selector(init));
objc_autorelease(obj);
objc_autoreleasePoolPop(pool);
```

源代码 2 - 类方法创建

```objc
@autoreleasepool {
	id __autorelease obj = [NSMutableArray array];
}
```

```objc
// 编译器模拟
id pool = objc_autoreleasePoolPush();
id obj = objc_msgSend(NSMutableArray, @selector(array));
objc_retainAutoreleasedReturnValue(obj);
objc_autorelease(obj);
objc_autoreleasePoolPop(pool);
```

