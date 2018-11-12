# 函数

```go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```

## 函数形式参数省略

上面的 `add` 函数可以写成

```go
func add(x, y int) int {
	return x + y
}
```

- 只有当连续两个或以上的形参类型相同时可以使用

## 多值返回

可以返回任意数量的返回值

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
    return y, x
}

func main() {
    a, b := swap("hello", "world")
    fmt.Println(a, b)
}
```

## 命名返回值

可以对返回值进行命名

```go
package main

import "fmt"

func split(sum int) (x, y int) {
    // x, y 变量与已命名的返回值名字相同，直接使用 return 则返回它们的值
    x = sum * 4 / 9
    y = sum - x
    return
}
```

