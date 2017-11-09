# JavaScript 中改变执行上下文的方法

## 一个例子引发的思考，求最小值

```js
Math.min(1, 2, 3); // 1

const nums = [1, 2, 3];
Math.min(nums); // NaN
```

同样的数据，为什么会出现不同的结果？

> Math.max() 与 Math.min() 方法期待的参数是分割开的数据，而不是包装在一个数组中的数据

解决方法

1. 使用 ES6 的特性，解构运算符

    ```js
    const nums = [1, 2, 3];
    Math.min(...nums); // 1
    
    // 等同于
    Math.min(1, 2, 3);
    ```

2. 使用更原始的方法解决, apply

    ```js
    var nums = [1, 2, 3];
    Math.nums.apply(Math, nums); // 1
    
    // 或者
    
    Math.nums.apply(null, nums); // 1
    ```


## 改变执行上下文的方法

### call() 与 apply()

始于 ES3

`call()` 与 `apply()` 的思想一样，只是使用上有细微的差别

`aFunction.call()` 与 `aFunction.apply()` 中传入的第一个参数，为 aFunction 的执行上下文，其余的参数作为 aFunction 的使用的参数

#### call()

事例代码

```js
aFunction.call(context, 1, 2, 3);
```

等同于下面的代码

```js
context.temp = aFunction; // 在 context 对象中创建一个临时属性存放方法
context.temp(1, 2, 3); // 调用该属性（这个属性就是方法），也就是调用了 aFunction
delete context.temp; // 把临时属性删除
```

#### apply()

事例代码

```js
aFunction.apply(context, [1, 2, 3]);
```

相同的代码逻辑如上面的 call() 一样

但是，可以看到，指定上下文之外的参数的传递方式，`call()` 方法是分隔开的， 而 `apply` 是存放到一个数组中传递

于是，可以知道，本文开头的列子，为什么使用了 apply, 而不是 call 了。因为传入的参数是一个数组


### bind()

始于 ES6

将函数 aFunction 绑定到某个对象。

当在函数 aFunction 上调用 `bind` 方法，并传入一个对象 context 参数时，`bind` 方法会返回一个新的函数 newFunction. 以函数方法调用 newFunction, 原始的函数 aFunction 就会以 context 为上下文进行调用

事例代码

```js
function f(y) {
    return this.x + y;
}

var o = {
    x: 1,
};

var g = f.bind(o);

g(2); // 3
```

在函数 g 中，其中的 f 函数体中的 `this` 就可以看作为是 o 了

使用 ES3 进行 `bind` 函数的实现

```js
function bind(aFunction, context) {
    if (aFunction.bind) return aFunction.bind(context); // 如果支持 bind 方法，直接返回
    else return function() {
        return aFunction.apply(context, arguments);
    };
}
```

最后的 `else` 条件中的实现，体现了 `bind` 方法是返回函数的方法，如果直接

```js
return aFunction.apply(context, arguments);
```

的话，就是把 aFunction 执行了，然而，这个 aFunction 需要之后再手动调用

所以，也可以看作

```js
function bind(aFunction, context) {
    if (aFunction.bind) return aFunction.bind(context); // 如果支持 bind 方法，直接返回
    else {
        var newFunction = function() {
            aFunction.apply(context, arguments);
        };
        
        return newFunction;
    }
}
```

> 部分代码来自 「JavaScript 权威指南」 P189

