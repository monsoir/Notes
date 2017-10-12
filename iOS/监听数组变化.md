# NSMutableArray 添加 KVO

## 使用代理 mutableArrayValueForKey:

1. 注册对 `NSMutableArray` 类型属性的监听
2. 实现方法


    ```swift
    - (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
    ```
3. 通过 `mutableArrayValueForKey` 访问数组

这样，每次向数组添加一个元素都会出发一次 KVO 的执行

但是，如果一次向数组中添加 N 个元素，也会出发 N 次 KVO 的执行，有时，这并不是我们想要的

一次向数组添加 N 个元素，但要使 KVO 执行一次，需要使用另外一个 API

```
- (void)insertObjects:(NSArray *)objects atIndexes:(NSIndexSet *)indexes
```

## 使用集合 KVO 规范

实现接口

- 添加

    ```swift
    insertObject:in<Key>AtIndex:
    
    // 或
    
    insert<Key>:atIndexes:
    ```

- 删除

    ```swift
    removeObjectFrom<Key>AtIndex:
    
    // 或
    
    remove<Key>AtIndexes:
    ```

- 替换，可选，可以在性能检测出现问题时实现

    ```swift
    replaceObjectIn<Key>AtIndex:withObject:
    
    // 或
    
    replace<Key>AtIndexes:with<Key>:
    ```

## 对比

以上两种方法监听 `NSMutableArray` 的方式非常类似，特别是在监听处的操作，几乎是一样：

1. 调用 `addObserver:forKeyPath:options:context:` 方法注册监听
2. 实现 `observeValueForKeyPath:ofObject:change:context:` 定义监听操作
3. 销毁时卸载监听

主要的不同在于 `NSMutableArray` 代理类的实现

1. 方法 1 需要自己再对数组进行修饰，并且当一次过添加多个元素时，可能会引起多次的监听回调，所以需要自己再对一次添加多个元素的情况进行分类处理
2. 方法 2 则只需要实现 KVO 的代理方法即可，并且当一次添加多个元素时，只会调用一次监听回调

## 对于 Swift 而言

由于 Swift 没有 KVO 接口规范的实现，因此，在 Swift 中监听数组内元素的变化，只有使用代理对象

同时，需要注意的是，被监听的数组，需要加上 `dynamic` 修饰符，才能成功监听到

## References

- [http://liumh.com/2015/08/22/ios-nsmutablearray-kvo/](http://liumh.com/2015/08/22/ios-nsmutablearray-kvo/)
- [https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/DefiningCollectionMethods.html#//apple_ref/doc/uid/10000107i-CH17-SW1](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/DefiningCollectionMethods.html#//apple_ref/doc/uid/10000107i-CH17-SW1)
- [关于在 Swift 中监听数组变化 https://stackoverflow.com/a/29411049/5211544](https://stackoverflow.com/a/29411049/5211544)

