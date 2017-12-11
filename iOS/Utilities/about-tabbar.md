# Tab bar 相关

## 显示与隐藏 tab bar

- 页面跳转，ControllerA -> ControllerB
- ControllerA 中，tab bar 显示，ControllerB 中，tab bar 隐藏
- 且保留边缘返回的手势等其他原有的导航控制器操作

```swift
// 在 ControllerA 跳转 ControllerB 的地方
hidesBottomBarWhenPushed = true
navigationController?.pushViewController(vc, animated: true)
hidesBottomBarWhenPushed = false
```

## 隐藏 tab bar 上的分割线

```swift
<#a tab bar#>.tabBar.shadowImage = UIImage()
<#a tab bar#>.tabBar.backgroundImage = UIImage()
```

