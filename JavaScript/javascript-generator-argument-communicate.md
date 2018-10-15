# JavaScript Generator 消息传递系统

## 参数传入

```js
function *foo(x, y) {
  return x * y;
}

const it = foo(6, 7);
const result = it.next();
console.log(result.value);
```

- 生成器只是一个比较特殊的函数，特殊的地方在于
    - 调用生成器函数时，它并不会执行函数中的逻辑，而是返回一个迭代器对象
    - 我们需要调用迭代器对象上的 `next()` 方法才会运行函数中的逻辑
- 调用 `next()` 后，也是会返回一个对象，其数据结构是

    ```js
    {
      value: aValue,
      done: true,
    }
    ```

    - `value` 是使用 `return` 或 `yield` 返回的值，如果没有返回，则像普通函数一样返回 `undefined`
    - `done` 是指明生成器中的逻辑是否全部完成
        - 像上面的例子，由于函数的逻辑立刻就返回 `return` , 所以执行后， `result.done` 为 `true`


## 生成器的数据输入与输出

```js
function *foo(x) {
  const y = x * (yield);
  return y;
}

const it = foo(6);
it.next();

const result = it.next(7);
console.log(result.value);
```

- 调用 `foo` 时，我们像调用普通函数一样调用，并传入了一个参数 `6` ，但由于生成器的原因，函数返回了一个迭代器对象
- 当调用 `it.next()` 时，函数体运行到 `yield` 处后暂停，同时等待一个数据的输入
- 当我们再次调用 `it.next(7)` 时， `7` 被传递到函数体中的 `yield` 处，同时，函数体继续往下运行，并返回结果

在这里，函数体中的 yield 与调用时的 next 两个方法，共同构成了生成器的双向消息传递的机制

当函数体中含有 `yield` 时，即不是立即返回的 `return` , 那么，当我们调用了函数，获取到迭代器对象后，若需要获取到结果，则至少需要调用两次 `next` 方法

- 第一次调用是启动函数体逻辑的执行，逻辑将会运行到 `yield` 处并暂停，等待下一次的 `next` 调用及其传入的参数（如果需要），若有需要，在 `yield` 后可以添加数据，作为此次调用的返回数据进行返回
- 第二次调用 `next` , 同时将需要的数据作为参数传入到 `next` 中（如果需要），此时，函数体将从上一次暂停的地方继续执行，并将 `next` 中的参数代入到 `yield` 的位置

```js
function *foo(x) {
  const y = x * (yield 'hello'); // 在 yield 时，同时会返回 hello 字符串
  return y;
}

const it = foo(6); // 传入参数 x = 6，获取到迭代器对象
let res = it.next(); // 第一次调用 next, 函数体运行到 yield 处暂停，同时，函数体将会返回 yield 后的数据
console.log(res.value); // hello

res = it.next(7); // 第二次调用 next, 函数体从上次暂停的地方继续进行，同时，7 将会代入到函数体中 yield 的位置
console.log(res.value); // 6 * 7 = 42
```


