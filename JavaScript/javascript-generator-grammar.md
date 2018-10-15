# JavaScript Generator 语法

生成器实际上只是一个普通的函数，只是函数签名前面加了 `*`

下面三种生成器的写法效果一样

```js
let x = 1;

// * 贴近函数签名
function *foo() {
  x++;
  yield;
  console.log("x: ", x);
}
```

```js
let x = 1;

// * 贴近 function 关键字
function* foo() {
  x++;
  yield;
  console.log("x: ", x);
}
```

```js
let x = 1;

// function, *, 函数签名间不添加空格
function*foo() {
  x++;
  yield;
  console.log("x: ", x);
}
```

