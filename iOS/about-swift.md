# Swift 语言相关

## 在方法中使用局部静态变量

在 Objective-C 中，在方法中使用局部静态变量，可以直接一个 `static` 来修饰，但是在 Swift 中这行不通，需要通过将变量存储在 `struct` 中

```swift
struct template {
    static let promptTemplate = "xxx"
    static let logTemplate = "xxx"
}
```

