# JavaScript try...finally

- 正常流程

```js
function foo() {
  try {
    return 42; // 将返回结果暂存，执行 finally 块
  }
  finally {
    console.log('something'); // 执行完成后，返回上面暂存值
  }

  console.log('never runs'); // 不会执行
}

console.log(foo()); // outputs: 42
```

- 抛出异常流程

```js
function foo() {
  try {
    throw 42; // 将异常暂存，执行 finally 块
  }
  finally {
    console.log('something'); // 执行完成后，将上面的异常抛出
  }

  console.log('never runs'); // 不会执行
}

console.log(foo());

// outputs:
// something
// [exception messages...]
```

- finally 块中抛出异常

```js
function foo() {
  try {
    return 42; // 暂存返回结果，执行 finally 块
  }
  finally {
    throw 'Oops'; // 上面暂存的返回结果被抛弃，函数执行在此处停止，并向你抛出一个异常
  }

  console.log('never runs'); // 不会执行
}

console.log(foo());

// outputs: [exception messages...]
```

- 返回值被覆盖

```js

// 返回值没有被覆盖
function foo() {
  try {
    return 42;
  }
  finally {
    // 这里没有返回任何值，所以返回值正常
  }
}

// 返回值被覆盖
function baz() {
  try {
    return 42;
  }
  finally {
    return 100; // 覆盖上面的返回值
  }
}

// 返回空的，暂存的返回值也会覆盖
function bar() {
  try {
    return 42;
  }
  finally {
    return; // 没东西，也会覆盖上面的返回值
  }
}

console.log(foo()); // 42
console.log(baz()); // 100
console.log(bar()); // undefined
```

