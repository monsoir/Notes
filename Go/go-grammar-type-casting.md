# 类型转换

公式 `T(v)`, 将值 v 的类型转换为 T

Go 在类型转换时，需要**显式转换**

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(i)

// ---

i := 42
f := float64(i)
u := uint(f)
```

