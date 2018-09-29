# JavaScript Promise 构造器 API

```js
const aPromise = new Promise(function(resolve, reject) {
  // some successful circumstances
  resolve(aValue);
    
  // some failed circumstances
  reject(err);
});
```

- 创建一个 Promise, 必须与 `new` 一起使用，同时提供一个回调函数
    - 更准确地说，是构建一个 pending 状态的 Promise
- 回调函数是要同步执行的
- reject 参数，就是用来拒绝当前的 Promise
- resolve 参数，可能是完成当前 Promise, 也可能是拒绝当前 Promise，根据传入的参数而定
- 当调用 resolve 时，`aValue` 是立即值时，当前 Promise 就会完成当前 Promise 并返回这个立即值
- 当跳用 resolve 时，`aValue` 是一个 Promise 或 thenable 时，`aValue` 会被递归展开，一直到立即值，传入的 Promise 也是需要使用构造器构造出来的

## 构建非 pending 的 Promise

创建一个已被拒绝的 Promise, 有两种方法

```js
const rejectedPromiseA = new Promise(function(resolve, reject) {
  reject(err);
});
    
const rejectedPromiseB = Promise.reject(err);
```

- `rejectedPromiseA` 与 `rejectedPromiseB` 是等价的
- 使用 `Promise.reject(...)` 创建已被拒绝的 Promise 更加简便清晰

创建一个已完成的 Promise

```js
const resolvedTh = {
  then: function(callback) {
    // do something
  },
};
    
const rejectedTh = {
  then: function(err, callback) {
    // do something with err or callback
  },
};
    
const promiseA = Promise.resolve(resovledTh);
const promiseB = Promise.resolve(rejectedTh);
```

- 上述代码是使用一个包含 `then` 函数的对象来创建 Promise, 即 `thenable`
- 使用 `Promise.resolve` 来创建一个已完成的 Promise
- 已完成，实际上是可以指代执行成功和执行失败的其中一种状态，取决于 then 的处理
- 如果创建已完成的 Promise 时，传入了规范的 Promise 对象，那么，会直接返回这个 Promise

