# JavaScript 中对象的装箱与拆箱

## 对象封装

- 对于基本数据类型，当访问 `toString()`, `.length` 方法时，会自动将值转换为对象
    - 但我们不需要提前优化，JS 引擎已经为我们做好优化工作
- 自行封装
    - 使用 `new ObjectType()`
    - 使用 `Object()` 函数

        ```js
        let a = 'abc'; // a 是基本数据类型，字符串
        let aObject = Object(a); // aObject 是对象类型，字符串
        ```

## 对象拆箱

- 获取到封装对象中的基本数据类型数据，使用 `valueOf()` 方法

    ```js
    let A = new String('abc'); // A 是对象类型，字符串
    let a = A.valueOf(); // a 是基本数据类型，字符串
    ```

