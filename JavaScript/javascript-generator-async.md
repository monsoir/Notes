# JavaScripot Generator 在异步中的使用

```js
function foo(x, y) {
  ajax(`https://a.web.site/blah/blah?x=${x}&y=${y}`, function(err, data) {
    if (err) {
      it.throw(err);
    }
    it.next(data);
  });
}

function *main() {
  try {
    const text = yield foo(11, 31);
    console.log(text);
  } catch (err) {
    console.log(err);
  }
}

// 获取迭代器
const it = main();

// 启动生成器
it.next();
```

若用以前的方式调用 `foo` , 返回的结果是 `undefined`

```js
const text = foo(11, 13);
// undefined
```

但这里使用了 `yield foo()` 的方式来进行调用，当运行时遇到 `yield` 时，将会执行后面的语句，即 `foo()` ，并以其结果作为返回值，然而，该返回值并不是返回给 `text` , 而是返回给调用处，即 `it.next()` , 而这里，同时发起了一个 **异步操作**

当 ajax 请求获得了数据后，再次调用了 `it.next()` , 同时，将获取到的数据 `data` 作为 `next` 的参数传入，继续 `main()` 的流程，此时的 `data` 便赋值给 `text`

因此，在使用生成器实现异步操作时，需要注意的是，异步操作需要的数据，是通过调用 next 时传入的

同时，使用生成器实现的异步操作，其异常捕获可以以同步的形式进行

```js
try {
	aIterator.next(aValue);
} catch (err) {
  // err
}
```

