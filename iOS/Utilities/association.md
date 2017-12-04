# 关联对象

## 代码

通用型

```swift
import Foundation

// for 数据类型
func associatedValue<ValueType: Any>(base: AnyObject, key: UnsafePointer<UInt8>, initialiser: () -> ValueType) -> ValueType {
    if let associated = objc_getAssociatedObject(base, key) as? ValueType { return associated }
    let associated = initialiser()
    objc_setAssociatedObject(base, key, associated, .OBJC_ASSOCIATION_ASSIGN)
    return associated
}

func associateValue<ValueType: Any>(base: AnyObject, key: UnsafePointer<UInt8>, value: ValueType) {
    objc_setAssociatedObject(base, key, value, .OBJC_ASSOCIATION_ASSIGN)
}

// for 对象类型
func associatedObject<ValueType: Any>(base: AnyObject, key: UnsafePointer<UInt8>, initialiser: () -> ValueType) -> ValueType {
    if let associated = objc_getAssociatedObject(base, key) as? ValueType { return associated }
    let associated = initialiser()
    objc_setAssociatedObject(base, key, associated, .OBJC_ASSOCIATION_RETAIN)
    return associated
}

func associateObject<ValueType: Any>(base: AnyObject, key: UnsafePointer<UInt8>, value: ValueType) {
    objc_setAssociatedObject(base, key, value, .OBJC_ASSOCIATION_RETAIN)
}

// source: http://swift.gg/2016/10/11/swift-extensions-can-add-stored-properties/
```

## 例子

```swift
private var DxContextKey: UInt8 = 1

extension UITextField {
    var dx: CGFloat {
        get {
            return associatedValue(base: self, key: &DxContextKey, initialiser: { () -> CGFloat in
                return 0
            })
        }
        
        set {
            associateValue(base: self, key: &DxContextKey, value: newValue)
        }
    }
}
```

