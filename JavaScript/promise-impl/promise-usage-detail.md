# Promise 的使用细节

使用方法 A

```js
const promiseA = new Promise((fulfill, reject) => {
  fulfill('here');
});

promiseA.then((value) => {
  console.log(`${value} a`);
});

promiseA.then((value) => {
  console.log(`${value} b`);
});
```

使用方法 B

```js
const promiseB = new Promise((fulfill, reject) => { fulfill('here'); })
                       .then((value) => { console.log(`${value} a`); })
                       .then((value) => { console.log(`${value} b`); });
```

使用 Promise 时，可以使用以上两种的形式进行调用

方法 A 和方法 B 看上去做的事情都是一样，都是根据等新建出来的 Promise 中的异步过程完成后，继续后面的工作，但实际上，这两种方法的执行机制是不同的

方法 A 的输出

```
here a
here b
```

方法 B 的输出

```
here a
undefined b
```

## 工作原理

当我们新建一个 Promise 对象 p 后，p 中实际上是有两个数组来保存回调的方法，两个数组分别保存异步操作成功后的回调，异步操作失败后的回调

而当在 p 上调用 `then` 方法时，成功回调和失败回调就会分别保存到对应的数组上，或者说是一个队列上。同时，`then` 方法的调用结果是返回一个新的 Promise 对象。没错，一个新的 Promise 对象，是与 p 完全不一样的对象，这是规范中写明的

因此，对于使用方法 A

  - 对 `promiseA` 调用了两次 `then` 方法，但实际上都是在同一个对象上进行操作
  - 因为调用了两次的 `then` 方法，所以 `promiseA` 中保存异步操作成功的回调队列就有两个元素
  - 由于操作的是同一个 `promiseA`, 同一个对象，所以两次 `then` 的回调的 `value` 都是一样的数据

而，对于使用方法 B

实际效果等同于

```js
const promiseB = new Promise((fulfill, reject) => { fulfill('here'); });
const promiseB1 = promiseB.then((value) => { console.log(`${value} a`); });
const promiseB2 = promiseB1.then((value) => { console.log(`${value} b`); });
```

  - 这种就是叫做「链式调用」，这是由于创建 `Promise` 和调用 `Promise` 的 `then` 方法两种情况下，都会返回一个新的 `Promise` 对象
    - 注意是新的 `Promise` 对象，而不是之前（或者是当前）的 `Promise` 对象
  - 两次调用 `then` 方法后，`promiseB` 和 `promiseB1` 中的异步成功回调队列都会有元素，但各自都曾经只有过一个
  - 由于 `promiseB`, `promiseB1` 和 `promiseB2` 都是不同的对象（引用不同），它们内部维护的 `value` 也因此是不同的。因此，第一次调用 `then` 中的 `value` 与第二次调用 `then` 中的 `value` 是两个不同的东西
    - 因为第一次调用 `then` 时，最后没有返回任何东西，因此导致了第二次调用 `then` 时的 `value` 为 `undefined`

如果想要在使用方法 B 中的第二次调用 `then` 时，`value` 有值，那么就需要在第一次调用 `then` 时返回数据，即

```js
const promiseB = new Promise((fulfill, reject) => { fulfill('here'); })
                       .then((value) => { console.log(`${value} a`); return 'there'; })
                       .then((value) => { console.log(`${value} b`); });
```

此时，的输出结果为

```
here a
there b
```

---

- 使用方法 A 就像并联，`value` 就像电压一样，处处相等
- 而使用方法 B 就像串联，串联下来的每个 `Promise` 实例就像电阻一样，`value` 像电压一样，`value` 不一样相等
    - 由于链式调用中的每一个 `then` 的返回结果都是不同，同时又会影响到下一个 `then` 接收的数据，因此可以使用链式调用来对数据进行加工

## References

- [Promises/A+](https://promisesaplus.com)


