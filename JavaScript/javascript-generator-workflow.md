# JavaScript Generator 工作流程

```js
let x = 1;

// * 贴近函数签名
function *foo() {
  x++;
  yield;
  console.log("x: ", x);
}

const it = foo(); // 1
it.next(); // 2
console.log(x); // 3

it.next(); // 4
console.log(x); // 5
```

过程解释

1. 获取到一个迭代器 `it` 对象，以后都是使用此对象来运行生成器的工作流程
2. 启动生成器，生成器内部运行到 `yield` 处后，暂停
3. 此时 x = 2
4. 再次启动生成器，生成器从 2 中暂停的地方继续运行：打印出 x 的值后
5. 此时 x = 2, 因为生成器中的流程已经走完了

每次调用 `it.next()` 时，如果流程没有结束的话，都会从上一次暂停的地方(`yield`)后的语句开始执行

