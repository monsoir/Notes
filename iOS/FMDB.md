# FMDB

## Error calling sqlite3_step (21: out of memory)

原源代码

```swift
do {
  let queryResult = try self.sharedDataBase?.executeQuery(sql, values: nil)
  while queryResult?.next() != nil { // <------ 就是这里
      let content = queryResult?.string(forColumn: "content")
      let dateTimeInterval = queryResult?.string(forColumn: "time_stamp")
      let historyItem = HistoryItem(with: content, dateTimeInterval: dateTimeInterval)
      history.append(historyItem)
  }
} catch let error as NSError {
  print("fetch all content error: \(error.localizedDescription)")
}
```

出错原因：在 Swift 中，queryResult?.next() 返回了一个 Wrapped 的 Bool 值，因此直接判断是否与 nil 相等是永远为 true，因此造成了死循环，记得 Unwrap！

```swift
do {
  let queryResult = try self.sharedDataBase?.executeQuery(sql, values: nil)
  while (queryResult?.next())! { // <------- 记得 Unwrap
      let content = queryResult?.string(forColumn: "content")
      let dateTimeInterval = queryResult?.string(forColumn: "time_stamp")
      let historyItem = HistoryItem(with: content, dateTimeInterval: dateTimeInterval)
      history.append(historyItem)
  }
} catch let error as NSError {
  print("fetch all content error: \(error.localizedDescription)")
}
```

