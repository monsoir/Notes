# 流程控制

## for

Go 中只有 `for` 循环

```go
package main

import "fmt"

func main() {
  sum := 0
  for i := 0; i < 10; i++ {
    sum += i
  }
  fmt.Println(sum)
}
```

### while

Go 中只有 `for` 循环，将 `for` 当 `while` 使用

```go
package main

import "fmt"

func main() {
  sum := 1
  for sum < 1000 {
    sum += sum
  }
  fmt.Println(sum)
}
```

### 无限循环

```go
func main() {
  for {
    // ....
    // for 中省略所有语句
  }
}
```

## if

```go
package main

import (
	"fmt"
	"math"
)

func sqrt(x float64) string {
	if x < 0 { // 不需要小括号
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	fmt.Println(sqrt(2), sqrt(-4))
}
```

### switch

```go
func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin": // 1.
		fmt.Println("OS X.") // 2.
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.", os)
	}
}
```

1. `case` 值不需要为常量，可以为多种类型
2. 不需要手动添加 `break` 语句，只会匹配一个，除非使用 `fallthrough`


### 没有 case 的 switch

```go
func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
```

- 可以代替 `if-then-else` 结构的判断，代码更清晰

## defer

- 将函数推迟到外层函数返回后执行
- 推迟调用的函数，其实会立即求值，只是直到外层函数返回前，函数都不会被调用
- 推迟的函数会被压入栈，遵从**后进先出**的原则

```go
func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}

// output:
// hello
// world
```

