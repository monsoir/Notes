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

## 隐藏 navigationBar, 恢复边缘侧滑返回

- 页面跳转：ControlleA -> ControllerB
- ControllerA 中显示导航栏
- ControllerB 中隐藏导航栏
- 且在系统提供的边缘侧滑返回手势进行中，导航栏不会在 ControllerA 与 ControllerB 中重叠，即还是 ControllerA 中显示导航栏，ControllerB 中隐藏导航栏

1. ControllerB 的生命周期方法中设置导航栏的显示与隐藏

    ```swift
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        navigationController?.setNavigationBarHidden(true, animated: true)
            
        // 开启隐藏导航栏后的边缘侧滑返回
        navigationController?.interactivePopGestureRecognizer?.isEnabled = true
        navigationController?.interactivePopGestureRecognizer?.delegate = self
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        navigationController?.setNavigationBarHidden(false, animated: true)
    }
    ```

2. ControllerB 中重新实现边缘侧滑返回的手势代理，实现 `UIGestureRecognizerDelegate` 协议

    ```swift
    extension NewReminderController: UIGestureRecognizerDelegate {
        func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldReceive touch: UITouch) -> Bool {
            // 开启隐藏导航栏后的边缘侧滑返回
            if gestureRecognizer == navigationController?.interactivePopGestureRecognizer {
                return true
            }
            
            return true
        }
    }
    ```

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

## 隐藏导航栏下的分割线

```swift
<#a navigation bar#>.navigationBar.shadowImage = UIImage()
```


