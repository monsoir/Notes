# 代理 Proxy

整体下来，感觉 JavaScript 的 Proxy 实际上是提供了一个简便的方式（或者说是通常的做法）来修改对象属性特性的途径

## 相关术语

### traps

直译陷阱，实际上是类似对象属性特性的访问方法，也可以看成是钩子

### handler

是一个对象，但这个对象的属性，需要包含 traps 的方法，这些方法会在合适的时机调用，即实现了钩子方法，这些方法会在对应的时机执行

### target

多种方式来描述：

- 为代理对象提供实际数据的对象
- 代理对象的数据后端
- 假设 P 为 A 的代理对象，则 A 是 P 的 target

## 语法

```js
const p = new Proxy(target, handler);
```

- 参数 `target` 即上面介绍的 target, 代理对象的数据后端
- 参数 `handler` 即上面的介绍的 handler, 需要包含“钩子”方法

全面的“钩子”方法，见 [Methods of the handler object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

## 例子

### 使用 Proxy 读取未定义属性的默认值

一般来说，若读取了对象中一个未定义的属性，那么读取的结果将会返回 `undefined`, 可以使用 Proxy 来返回一个默认值，此处假设为 1000

```js
const target = {};
console.log(target.a); // undefined, a 未定义
console.log(target.b); // undefined, b 未定义

const handler = {
  get: function(obj, prop) {
    return prop in obj ? obj[prop] : 1000
  },
};

const aProxy = new Proxy(target, handler);
aProxy.a = 1;

console.log(aProxy.a); // 1, a 定义了，并赋值了
console.log(aProxy.b); // 1000, b 还是未定义，但还是有值出来了
```

### 使用 Proxy 有条件设置属性值

给一个对象赋值时，需要根据一定条件进行赋值，此时，一般的做法在赋值语句周边写各种条件判断，但我们也可以将这些判断条件融合到赋值操作中，即我们调用时，只管赋值，不管条件是否满足

下面例子，向 target 对象赋值，成功赋值的情况只有新值不大于 200, 大于 200 或者新值类型不是数字类型的情况，将抛出异常

```js
const handler = {
  set: function(obj, prop, value) {
    if (prop === 'a') {
      if (!Number.isInteger(value)) throw new TypeError("value should be a Number type");
      if (value > 200) throw new RangeError("value should be less than 200");
    }
	
    obj[prop] = value;
    return true;
  },
};

const target = {
  a: 0,
};

const aProxy = new Proxy(target, handler);
aProxy.a = 199;
console.log(aProxy.a); // 199, 成功设置

aProxy.a = 201; // 抛出异常，新值大于了 200
aProxy.a = "abc"; // 抛出异常，新值不是数字类型
```

## References

- [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

