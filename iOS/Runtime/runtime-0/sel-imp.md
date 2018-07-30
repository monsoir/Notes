# SEL 与 IMP

SEL 即方法的 ID, 关于 SEL 的定义

```objc
typedef struct objc_selector *SEL;

struct objc_selector {
    char *name;                       OBJC2_UNAVAILABLE;
    char *types;                      OBJC2_UNAVAILABLE;
};
```

IMP 是函数指针，指向了函数的最终实现

SEL 与 IMP 的关系可以类比字典中的 Key 与 Value 的关系

> OC 不支持函数重载，这是由于 SEL 只能是唯一的，即对比字典中的 Key
> 但在不同的类中，可以有相同的 SEL, 这是由于每个类都维护着不同的「字典」，SEL 处于不同的空间


