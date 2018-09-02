# JavaScript 类型转换内置方法

## `toString()`

当对象需要强转成字符串时，会自动调用此方法

通过重写此方法，可以设置自定义的字符串输出，而不是 `[object Object]`

```js
const s = {
  x: 7,
  y: 3,
  toString: function() {
    return `x: ${this.x}; y: ${this.y}`;
  },
};

console.log(`${s}`);

// output:
// x: 7; y: 3
```

## `valueOf()`

当对象需要被强转成基本类型时，如 `number`, 则会自动调用此方法

```js
const s = {
  x: 7,
  y: 3,
  valueOf: function () {
    return this.x + this.y;
  },
};

console.log(+s);

// output:
// 10
```

## `[Symbol.toPrimitive]()`

实际上是 `toString()` 与 `valueOf()` 的取代方法

```js
const t = {
  x: 7,
  y: 3,
  [Symbol.toPrimitive]: function(hint) {
    // console.log(hint);
    if (hint === 'number') {
      return this.x + this.y;
    } else if (hint === 'string') {
      return `x + y = ${this.x + this.y}`;
    }
    
    return 'default';
  },
};

console.log(+t); // 10
console.log(`${t}`); // x + y = 10
console.log(t + ''); // default
```

## `toJSON()`

当需要将对象序列化时，会自动调用此方法将对象转换为 JSON 字符串

通常，可以重写此方法来将一个复杂的对象，抽取其中需要的数据组成对象来返回

```js
const z = {
  aa: 'abc',
  bb: 12,
  toJSON: function() {
    return {
      a: this.aa,
      b: this.bb,
    };
  },
};

console.log(JSON.stringify(z)); // {"a":"abc","b":12}
```

