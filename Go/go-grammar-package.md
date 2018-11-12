# 包

- 每个 Go 程序都是由包构成
- 约定：程序中使用的包名，与包导入路径的最后一个元素一致

    ```go
    package main
    
    import (
        "fmt" // 此文件中使用 fmt 包时，使用 fmt
        "math/rand" // 此文件中使用 math/rand 包时，使用 rand
    )
    ```
    
## 导入包

- ”分组“形式，使用圆括号

    ```go
    import (
        "fmt" // 此文件中使用 fmt 包时，使用 fmt
        "math/rand" // 此文件中使用 math/rand 包时，使用 rand
    )
    ```

- 多个导入语句

    ```go
    import "fmt"
    import "math"
    ```

## 导出包

- 如果一个东西的名字是以**大写字母**开头，那就是已到处的, 如 `Math.Pi`
- 导入包时，只能导入已导出的名字，否则无法访问



