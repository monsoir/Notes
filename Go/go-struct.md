# 结构体

在 Go 中，结构体 struct 表示一个字段的集合

```go
type Vertex struct {
  X int
  Y int
}

func main() {
  fmt.Println(Vertex{1, 2})
}
```

## 访问结构体成员

访问结构体成员，使用 `.` 运算符

```go
type Vertex struct {
  X int
  Y int
}

func main() {
  v := Vertex{1, 2}
  v.x = 4
  fmt.Println(v.x)
}
```

## 结构体指针

像上面的例子，如果有指针 `p` 所指向的值类型为 `Vertex`, 那可以通过一下两种方式通过指针进行获取值

```go
type Vertex struct {
  X int
  Y int
}

func main() {
  v := Vertex{1, 2}
  p := &v // 此时，p 为指向 Vetex 类型实例的 
  p.X = 10 // 使用点运算符，直接写
}
```

