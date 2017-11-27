# 文字编辑 UI 相关

## 触摸界面后，自动隐藏键盘

```swift
// Swift 4
extension UIViewController {
    func automaticallyDismissKeyboard() {
        let tapGesture = UITapGestureRecognizer(target: self, action: #selector(self.dismissKeyboard))
        view.addGestureRecognizer(tapGesture)
    }
    
    @objc private func dismissKeyboard() {
        view.endEditing(true)
    }
}
```

