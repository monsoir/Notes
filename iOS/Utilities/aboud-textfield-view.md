# TextField, TextView 相关

## 调整边缘间距

### TextField

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

### TextView

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

## TextView 凑合用 Placeholder

```swift
import UIKit

private var NeedPlaceholderContextKey: UInt8 = 0
private var PlaceholderContextKey: UInt8 = 1
extension UITextView: UITextViewDelegate {
    private var needPlaceholder: Bool {
        get {
            return associatedValue(base: self, key: &NeedPlaceholderContextKey, initialiser: { () -> Bool in
                return false
            })
        }
        
        set {
            associateValue(base: self, key: &NeedPlaceholderContextKey, value: newValue)
        }
    }
    
    private var placeholder: String {
        get {
            return associatedObject(base: self, key: &PlaceholderContextKey, initialiser: { () -> String in
                return ""
            })
        }
        
        set {
            associateObject(base: self, key: &PlaceholderContextKey, value: newValue)
        }
    }
    
    convenience init(placeholder: String) {
        self.init()
        setPlaceholder(placeholder: placeholder, shouldSet: true)
        self.textColor = .lightGray
        self.text = placeholder
    }
    
    func setPlaceholder(placeholder: String = "", shouldSet: Bool = false) {
        needPlaceholder = shouldSet
        if (shouldSet) {
            self.placeholder = placeholder
            delegate = self
        } else {
            self.placeholder = ""
            delegate = nil
        }
    }
    
    public func textViewDidBeginEditing(_ textView: UITextView) {
        if (self.needPlaceholder && textView.textColor == .lightGray) {
            textView.text = nil
            textView.textColor = .black
        }
    }
    
    public func textViewDidEndEditing(_ textView: UITextView) {
        if (self.needPlaceholder && textView.text.isEmpty) {
            textView.text = self.placeholder
            textView.textColor = .lightGray
        }
    }
}
```


