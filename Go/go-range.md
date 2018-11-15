# Range

结合 `for` 循环用于遍历切片或映射

```go
var pow = []int{1, 2, 3, 4, 5}

func main() {
    for index, value := range pow {
        // do something with index, value
    }
    
    for _, value := range pow {
        // do something with value
    }
}
```

- `for index, value := range pow` 中，`index` 为元素下标，`value` 为当前元素
- 可以通过 `_` 来省略不关心的变量命名

