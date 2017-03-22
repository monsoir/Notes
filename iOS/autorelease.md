[TOC]

# autorelease

## 简介

autorelease 当对象超出其作用域时，对象实例的 release 方法将被调用

## 使用方法

1. 生成并持有 NSAutoreleasePool 对象
2. 调用已分配对象的 autorelease 实例方法
3. 废弃 NSAutoreleasePool 对象

```objc
// 生成并持有 NSAutoreleasePool 对象
NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

// 调用已分配对象的 autorelease 实例方法
id obj = [[NSObject alloc] init];
[obj autorelease];

// 废弃 NSAutoreleasePool 对象
[pool drain]; // <=> [obj release];
```

## 与 NSRunLoop 的关系

程序主循环 NSRunLoop 每次循环过程中都有 NSAutoreleasePool 对象被生成或废弃

NSRunLoop

- 生成 NSAutoreleasePool 对象
- 应用程序主线程处理 
- 废弃 NSAutoreleasePool 对象

## 注意

当大量生成 autorelease 对象时，只要不废弃 NSAutoreleasePool 对象，那生成的对象也不会被释放，造成 **内存不足**。

- 如处理大量图片的时候            

因此，我们可以在处理占用大量内存的任务时，可以使用

```objc
@autoreleasepool {
	// 处理占用大量内存的任务
}
```

来进行包裹

同时要注意，编译器会检查方法名是否以 alloc/new/copy/mutableCopy 开始，若不是，则自动将返回值的对象注册到 autoreleasepool 中。
因此，若以 alloc/new/copy/mutableCopy 开始的方法创建的对象，可使用

```objc
@autoreleasepool {
	// 使用 __autorelease 进行修饰
	id __autorelease autoObj = [[SomeObject alloc] init];
}
```


