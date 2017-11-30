# 时间日期相关

## 根据系统时区，返回当时的时间

```swift
extension Date {
    func datetimeWithSystemTimeZone() -> Date {
        // 获取系统时区
        let timeZone = NSTimeZone.system
        // 获取系统时区与格林尼治时间的差，以秒为单位
        let timeInterval = timeZone.secondsFromGMT(for: self)
        // 将当前时间加上时间差
        return addingTimeInterval(TimeInterval(timeInterval))
    }
}
```

