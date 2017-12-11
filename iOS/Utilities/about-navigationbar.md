# NavigationBar 相关

## 隐藏 navigationBar, 而不失去边缘侧滑返回

✔︎

```swift
navigationController?.navigationBar.isHidden = true
```

✘

```swift
navigationController?.isNavigationBarHidden = true
```

[👉 From](https://stackoverflow.com/a/37092015/5211544)

## 设置 titleView 大小

iOS 11 后，手动设置 `titleView` 大小需要

1. 覆盖 view 的方法

    ```swift
    override var intrinsicContentSize: CGSize {
    //        return UILayoutFittingExpandedSize
        return CGSize(width: 500, height: 50)
    }
    ```

2. 设置不使用自动布局

    新建一个 view 之后，设置自动布局的属性
    
    ```swift
    view.translatesAutoresizingMaskIntoConstraints = false
    ```

