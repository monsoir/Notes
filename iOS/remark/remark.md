# 备忘

记录一下经常忘记的一些细节

## 自动布局

当手写 UI 代码，直接使用原生 API 进行自动布局时，记得将子视图的 `translatesAutoresizingMaskIntoConstraints` 设置为 `false`, 否则约束会产生冲突

`translatesAutoresizingMaskIntoConstraints` 的作用是将开发者设置的 frame 值自动转换为约束

而第三方布局库，如 SnapKit, Masonry 则是已经帮我们设置好了 `translatesAutoresizingMaskIntoConstraints = false`

---

在 iOS 8+, 使约束生效时，应调用类方法，它将会自动将约束添加到对应的 view 并激活约束

```swift
NSLayoutConstraint.activate(<约束组成的数组>)
```

---

