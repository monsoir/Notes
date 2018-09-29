# JavaScript Promise 控制

## Promise.all

门 Gate 机制

- 等待两个或多个并发/并行的任务完成后，才继续进一步的流程
- 这多个任务的完成顺序不重要，重要的是都完成了

    ```js
    const promise1 = PromiseA;
    const promise2 = PromiseB;
    
    Promise.all([promise1, promise2])
      .then(function(values) {
        // do something
      });
    ```

- values 是一个数组，其中 `promise1`, `promise2` 的值，同时值的顺序与 Promise 传入的顺序有关，与 Promise 完成顺序无关
- 当 `[promise1, promise2]` 中其中一个 promise 遭到了 reject 后，`Promise.all` 整个 Promise 都会被拒绝

## Promise.race

门闩，竞态

- 只响应第一个完成的 Promise, 同时抛弃其他 Promise

    ```js
    const promise1 = PromiseA;
    const promise2 = PromiseB;
    const immediateValue = 1;
    
    Promise.race([promise1, promise2, immediateValue])
      .then(function(value) {
        // do something
      });
    ```

- `Promise.race` 接受一个数组作为参数，数组中的元素可以为 Promise, thenable, 立即值
    - 但是立即值在竞争中没有意义，因为肯定是最快的
- 回调中的参数 `value` 只有一个值，即竞争中胜出的值

###  应用

超时竞赛

```js
Promise.race([foo(), timeoutPromise(3000)])
  .then(function() {
    // foo() 在竞争中胜出，在 3 秒内完成了
  }, function(err) {
    // foo() 执行失败，被拒绝了，或者没有在 3 秒内完成
  });
```

## Promise 变体

### Promise.none([...])

- 类似 `Promise.all([...])`
- 但与 Promise.all 相反，当 Promise 都被拒绝后，才会 resolve

### Promise.any([...])

- 类似 `Promise.all([...])`
- 但忽略被 reject 的 Promise, 即有 Promise 被 reject, 也会继续执行
- 只需完成多个 Promise 中的一个就可以 resolve

### Promise.first([...])

- 类似 `Promise.any` 的竞争条件，只要传入的第一个 Promise 完成，后续的拒绝和完成都会被忽略

### Promise.last([...])

- 类似 `Promise.first` 的竞争条件
- 但只需要传入的最后一个 Promise 完成




