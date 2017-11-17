# 调整边缘间距

## TextField

需要继承 TextField 来重写方法

```swift
// IndentedTextField.swift

class IndentedTextField: UITextField {
    var dx = CGFloat(10)
    var dy = CGFloat(10)
    
    // for placeholder
    override func textRect(forBounds bounds: CGRect) -> CGRect {
        return bounds.insetBy(dx: dx, dy: dy)
    }
    
    // for text
    override func editingRect(forBounds bounds: CGRect) -> CGRect {
        return bounds.insetBy(dx: dx, dy: dy)
    }
}
```

## TextView

直接设置属性 `textContainerInset` 即可

```swift
lazy var tvDetail: UITextView = {
    let view = UITextView()
//      view.backgroundColor = .orange
    view.tintColor = .red
    view.font = UIFont.systemFont(ofSize: 20)
    view.textContainerInset = UIEdgeInsetsMake(8, 8, 8, 8) // text view 改变内容 inset
    return view
}()
```


