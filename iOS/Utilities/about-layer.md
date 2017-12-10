# 图层相关

## 添加圆角

使用 BezierPath 实现圆角，同时可以指明圆角的的实现位置

```swift
func adjustCorner(_ bounds: CGRect) {
    // 指明圆角的位置，大小
    let maskPathAll = UIBezierPath(roundedRect: bounds, byRoundingCorners: .allCorners, cornerRadii: CGSize(width: RADIUS, height: RADIUS))
    let shapeLayerAll = CAShapeLayer()
    shapeLayerAll.frame = bounds
    shapeLayerAll.path = maskPathAll.cgPath
    view.layer.masksToBounds = true
    view.layer.mask = shapeLayerAll
}
```

## 添加阴影

```swift
self.layer.shadowOffset = CGSize(width: -2.0, height: -2.0) // 偏移值，向右，向下为正方向
self.layer.shadowRadius = 10
self.layer.shadowOpacity = 0.8
self.layer.shadowColor = UIColor(hexString: "#c7c7c7")!.cgColor
```


