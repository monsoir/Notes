# 类与对象的操作函数

- [获取类的函数](#获取类的函数)
- [动态创建类](#动态创建类)
- [动态创建对象](#动态创建对象)
- [实例操作函数](#实例操作函数)
- [获取类定义](#获取类定义)

Runtime 提供大量的函数，用来操作类与对象，其中

- 操作类的函数，大部分以 `class_` 为前缀
- 操作对象的函数，大部分以 `objc_` 或 `object_` 为前缀

## 操作类的函数

### 类名

获取类名

```c
const char * class_getName( Class cls );
```

### 父类与元类

获取父类

```c
Class class_getSuperclass(Class cls);
```

- `cls` 为 `nil` 或根类时，返回 `nil`

判断给定的类是否为元类

```c
BOOL class_isMetaClass(Class cls);
```

- `cls` 不是元类或为 `nil` 时，返回 `NO`

### 获取实例大小

```c
size_t class_getInstanceSize(class cls);
```

### 操作成员变量及属性

- 在 `objc_class` 中，所有成员变量，属性的信息，存放在链表 `ivars` 中 (上面对 `objc_class` 定义中有)
- `ivars` 是一个数组，其中的每个元素为 `Ivar` 类型的指针

---

获取类中指定的 **实例成员变量** 的信息，通过名称指定

```c
Ivar class_getInstanceVariable ( Class cls, const char *name );

typedef struct objc_ivar *Ivar;
```

获取 **类成员变量** 的信息

```c
Ivar class_getClassVariable ( Class cls, const char *name );
```

- 返回结果不包含父类的成员变量和属性

添加成员变量

```objc
BOOL class_addIvar ( Class cls, const char *name, size_t size, uint8_t alignment, const char *types );
```

- Objective-C 不支持向已存在的类中添加实例变量
- 这个函数却提供了在运行时动态创建一个类的能力（限制不写了，大概知道）

获取整个成员变量列表

```objc
Ivar * class_copyIvarList ( Class cls, unsigned int *outCount );
```

- 不包含在父类中声明的变量
- `outcount` 返回数组的大小
- **需要手动释放返回数组的空间**

---

获取指定属性

```objc
objc_property_t class_getProperty ( Class cls, const char *name );
```

获取属性列表

```objc
objc_property_t * class_copyPropertyList ( Class cls, unsigned int *outCount );
```

为类添加属性

```objc
BOOL class_addProperty ( Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount );
```

替换类的属性

```objc
void class_replaceProperty ( Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount );
```

### 操作方法

对 `objc_class` 中 `methodLists` 的操作

添加方法

```objc
BOOL class_addMethod ( Class cls, SEL name, IMP imp, const char *types );
```

- 会覆盖父类方法的实现
- 若本类包含了同名实现，函数返回 `NO`
- 修改已存在的实现，调用 `method_setImplementation`
- imp 所指向的函数实现，至少需要有两个参数 `self` 和 `_cmd`

    ```objc
    void myMethodIMP(id self, SEL _cmd)
    {
        // implementation ....
    }
    ```

- type 涉及传递给方法的参数类型的字符数组，涉及类型编码

获取实例方法

```objc
Method class_getInstanceMethod ( Class cls, SEL name );
```

- 会搜索父类中的实现

获取类方法

```objc
Method class_getClassMethod ( Class cls, SEL name );
```

- 会搜索父类中的实现

获取所有实例方法，包装在一个数组中

```objc
Method * class_copyMethodList ( Class cls, unsigned int *outCount );
```

- 不会搜索父类中的实现
- **需要手动释放返回数组的空间**
- 获取所有类方法，变换一下调用 `class_copyMethodList(object_getClass(cls), &count)`

替换方法的实现

```objc
IMP class_replaceMethod ( Class cls, SEL name, IMP imp, const char *types );
```

- 若类中不存在指定方法(name), 则添加
- 若存在，则替换

获取方法的具体实现

```objc
IMP class_getMethodImplementation ( Class cls, SEL name );
IMP class_getMethodImplementation_stret ( Class cls, SEL name );
```

- 前者会在向类实例发送消息时被调用，返回指向方法实现的函数指针
- 返回结果不一定是方法的实际实现，也可能是 Runtime 内部的一个函数，如：实例无法响应被调用方法，则返回的是消息转发机制的一部分

判断类实例是否响应指定的方法

```objc
BOOL class_respondsToSelector ( Class cls, SEL sel );
```

- 在 `NSObject` 中，通常使用的是 `respondsToSelector:` 或 `instancesRespondToSelector:`

### 操作协议

添加协议

```objc
BOOL class_addProtocol ( Class cls, Protocol *protocol );
```

判断类是否实现指定协议

```objc
BOOL class_conformsToProtocol ( Class cls, Protocol *protocol );
```

- 在 `NSObject` 世界中，使用 `conformsToProtocol:`

获取类实现的协议列表

```objc
Protocol * class_copyProtocolList ( Class cls, unsigned int *outCount );
```

- **需要手动释放返回数组的空间**

## 动态创建类

创建一个新类或元类

```objc
Class objc_allocateClassPair ( Class superclass, const char *name, size_t extraBytes );
```

- 创建根类时，`superclass` 需要指定为 `nil`
- `extraBytes` 分配给类对象尾部的索引 ivars 的字节数，一般为 0

动态创建类的步骤

1. 调用 `objc_allocateClassPair` 创建类
2. 调用 `class_addMethod`, `class_addIvar` 添加方法和实例变量或属性
3. 调用 `objc_registerClassPair` 注册类
4. 之后就可以使用类了

注册类

```objc
void objc_registerClassPair ( Class cls );
```

销毁一个类，及其相关的类

```objc
void objc_disposeClassPair ( Class cls );
```

## 动态创建对象

创建类实例

```objc
id class_createInstance ( Class cls, size_t extraBytes );
```

- `extraBytes` 表示分配的额外字节数，用于存储在类定义中所定义的实例变量之外的实例变量之外的变量，**ARC 下无法使用这个函数**

在指定位置创建类实例

```objc
id objc_constructInstance ( Class cls, void *bytes );
```

销毁类实例

```objc
void * objc_destructInstance ( id obj );
```

- 销毁类实例，但不会释放相关引用

## 实例操作函数

### 对整个对象的操作

返回指定对象的拷贝

```objc
id object_copy ( id obj, size_t size );
```

释放指定对象占用的内存

```objc
id object_dispose ( id obj );
```

### 对对象的实例变量的操作

改变类实例的实例变量的值

```objc
Ivar object_setInstanceVariable ( id obj, const char *name, void *value );
```

获取实例变量的值

```objc
Ivar object_getInstanceVariable ( id obj, const char *name, void **outValue );
```

获取指向给定对象分配的任何额外字节的指针

```objc
void * object_getIndexedIvars ( id obj );
```

获取对象中实例变量的值

```objc
id object_getIvar ( id obj, Ivar ivar );
```

设置对象中实例变量的值

```objc
void object_setIvar ( id obj, Ivar ivar, id value );
```


上面的函数，有些功能相似，函数名短的指定速度快

### 对对象的类进行操作

获取给定对象的类名

```objc
const char * object_getClassName ( id obj );
```

获取对象的类

```objc
Class object_getClass ( id obj );
```

设置对象的类

```objc
Class object_setClass ( id obj, Class cls );
```

## 获取类定义

Runtime 会自动注册我们代码中定义的所有类，也可以通过 `objc_addClass` 函数注册

获取已注册的类定义的列表

```objc
int objc_getClassList ( Class *buffer, int bufferCount );
```

- 返回的是已注册类的个数
- `buffer` 获取到的是列表，最后需要我们手动释放所占内存空间

创建并返回一个指向所有已注册类的指针列表

```objc
Class * objc_copyClassList ( unsigned int *outCount );
```

返回指定类的定义

```objc
Class objc_lookUpClass ( const char *name );
Class objc_getClass ( const char *name );
Class objc_getRequiredClass ( const char *name );
```

1. 类在运行时未注册，返回 `nil`
2. 类在运行时未注册，调用类处理回调，再次确认类是否注册，再未注册，返回 `nil`
3. 类在运行时未注册，杀死进程

获取指定类的元类

```objc
Class objc_getMetaClass ( const char *name );
```

- 类在运行时未注册，调用类处理回调，再次确认类是否注册，再未注册，返回 `nil`

