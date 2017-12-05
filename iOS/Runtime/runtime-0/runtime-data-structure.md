# 类与对象的基本数据结构

类与对象的基础数据类型有

- `Class` --> `objc_class`
- `id` --> `objc_object`
- `objc_cache`
- 元类 Meta Class

## Class

- Objective-C 类由 `Class` 类型表示
- 而这个 `Class` 类型实际上是一个指向 `objc_class` 结构体的指针

    ```c
    typedef struct objc_class *Class;
    ```
    
### objc_class 结构体定义

```c
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;
#if !__OBJC2__
    Class super_class                   	OBJC2_UNAVAILABLE;	// 父类
    const char *name                      	OBJC2_UNAVAILABLE;	// 类名
    long version                          	OBJC2_UNAVAILABLE;	// 类的版本信息，默认为0
    long info                            	OBJC2_UNAVAILABLE;	// 类信息，供运行期使用的一些位标识
    long instance_size                   	OBJC2_UNAVAILABLE;	// 该类的实例变量大小
    struct objc_ivar_list *ivars         	OBJC2_UNAVAILABLE;	// 该类的成员变量链表
    struct objc_method_list **methodLists 	OBJC2_UNAVAILABLE;	// 方法定义的链表
    struct objc_cache *cache              	OBJC2_UNAVAILABLE;	// 方法缓存
    struct objc_protocol_list *protocols 	OBJC2_UNAVAILABLE;	// 协议链表
#endif
} OBJC2_UNAVAILABLE;
```

`isa`

- 指向元类 `metaClass`, 所有的类本身也是一个对象，这个对象(Class类型)也有一个 `isa` 指针

`superClass`

- 指向类的父类
    - `NSObject` 与 `NSProxy` 的 `superClass` 为 `NULL`

`cache`

- 缓存最近使用的方法

`methodLists`

- 保存所有方法

## id

- 提供一种类似范型的操作
- id 实际上是 `objc_object` 结构类型的指针

```c
typedef struct objc_object *id;
```

### objc_object

每个类的实例被创建时，都会包含 `objc_object` 类型的数据

```c
struct objc_object {
    Class isa  OBJC_ISA_AVAILABILITY;
};
```

`isa`

- 指向实例的类的指针
- 当向对象发送消息时，Runtime 就会根据实例的 `isa` 指针找到所属的类，并在类的方法列表中找到相应的方法执行

> 进一步解释
> 假设有一个类 Dog, 而 dog 是一个对象，即对象 dog 是类 Dog 的一个实例
> 
> 根据上面的阐述，大概可以得出两个关系
> 1. Dog 也是一个 `Class` 类型的对象，包含了一个方法列表 `methodLists`, 这里就不考虑方法缓存的情况
> 2. dog 包含了一组类型为 `objc_object` 的数据，即 `isa`，而 `isa` 则是指向了 `Dog`
> 
> 当向实例 dog 发送消息时，如 `[dog go]`
> 1. Runtime 根据 dog 的 `isa` 所指向的内容，找到了 Dog
> 2. 在 Dog 中的方法列表 `methodList` 中找到了方法 go

## objc_cache

用于缓存调用过的方法

```c
struct objc_cache {
    unsigned int mask /* total = mask + 1 */                 OBJC2_UNAVAILABLE;
    unsigned int occupied                                    OBJC2_UNAVAILABLE;
    Method buckets[1]                                        OBJC2_UNAVAILABLE;
};
```

## 元类 

是什么

- 类自身也是一个对象
- 对象也就是一个 `objc_object` 指针
- `objc_object` 定义中包含了 `isa` 指针
- 类的 `isa` 所指向的，就是元类，类型为 `objc_class`

更多

- 元类存储了一个类的所有类方法
- 每个类都有单独的元类
- 任何元类的 `isa` 直接指向基类 `NSObject` 的元类
- 而 `NSObject` 的元类的 `isa` 则是则是指向自己 `NSObject`

