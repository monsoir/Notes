# UIAlertController

## 简单的告示弹窗

```swift
func commonAlert(title: String = "", detail: String = "") -> UIAlertController {
    let alert = UIAlertController(title: title, message: detail, preferredStyle: .alert)
    let action = UIAlertAction(title: "确定", style: .cancel, handler: nil)
    alert.addAction(action)
    return alert
}
```

