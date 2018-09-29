# JavaScript Promise 处理方法

```js
// p 是一个 Promise

p.then(fulfilled);

p.then(fulfilled, rejected);

// 以下两个一定程度上是等价的
p.then(null, rejected);
p.catch(rejected);
```

- 每个 Promise 实例都有 `then` 和 `catch` 方法
    - `then` 接受一个或两个参数，1st 用于完成回调，2nd 用于拒绝回调
    - 如果 `then` 的参数被忽略，或者传入的不是函数对象，则使用默认的回调
    - 完成的默认回调是继续把消息传递
    - 拒绝的默认回调是把错误向外抛出
    - `then` 与 `catch` 都会创建并返回一个 Promise

