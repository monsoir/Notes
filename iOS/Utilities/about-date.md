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

## 获取时间组件

```swift
func component(of component: Calendar.Component) -> Int {
    return Calendar.current.component(component, from: self)
}
```

## 获取时间的格式化字符串

```swift
/// 获取格式为 yyyy-MM-dd 的日期显示
///
/// - Returns: 易读的时间表示
func dateRepresentation() -> String {
    let formatter = DateFormatter()
    formatter.dateFormat = "yyyy-MM-dd"
    return formatter.string(from: self)
}
```

```swift
/// 获取格式为 HH:mm:ss 或 HH:mm 格式的时间显示，为24小时制
///
/// - Parameter needSecond: 是否需要显示秒数
/// - Returns: 表示时间的字符串
func timeRepresentation(needSecond: Bool = false) -> String {
    let formatter = DateFormatter()
    formatter.dateFormat = needSecond ? "HH:mm:ss" : "HH:mm"
    return formatter.string(from: self)
}
```

```swift
/// 获取日期格式为 yyyy-MM-dd 的日期字符串，与时间格式为 HH:mm:ss 的时间字符串所组成的元组
///
/// - Returns: 一个包含日期和时间字符串表示的元组
func representation(needSecond: Bool = false) -> (date: String, time: String) {
    return (self.dateRepresentation(), self.timeRepresentation(needSecond: needSecond))
}
```


