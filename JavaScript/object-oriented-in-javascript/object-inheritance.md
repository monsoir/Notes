# 对象继承

对象继承是 JavaScript 其中一种继承类型

不同于 C++, Java 中的继承，这种继承是直接指定对象实例的父对象，通过指定 `[[Prototype]]` 来实现

所有字面形式创建的对象，其 `[[Prototype]]` 都是 `Object.prototype`, 同时，可以通过使用 `Object.create()` 方法来显式指定

## Object.create()

`Object.create()` 接受两个参数

1. 新对象的 `[[Prototype]]`
2. 属性特征描述对象

### 最简单的例子

通过字面值创建的对象

```js
const anObject = {
  name: 'object',
};
```

通过 `Object.create()` 创建的对象

```js
const anObject = Object.create(Object.prototype, {
  name: {
    configurable: true,
    enumerable: true,
    value: 'object',
    writable: true,
  },
});
```

以上两种方式创建的对象效果是一样的，它们都是继承自 `Object.prototype`

### 实际一点的例子

```js
const objectA = {
  name: 'objectA',
  sayName: function() {
    console.log(this.name);
  },
};

const objectB = Object.create(objectA, {
  name: {
    configurable: true,
    enumerable: true,
    value: 'objectB',
    writable: true,
  },
});

objectA.sayName(); // objectA
objectB.sayName(); // objectB

console.log(objectA.hasOwnProperty('sayName')); // true
console.log(objectB.hasOwnProperty('sayName')); // false, sayName 是从 objectA 中继承过来的，不属于自有属性

// Object.prototype 是所有对象的原型对象
console.log(Object.prototype.isPrototypeOf(objectA)); // true
console.log(Object.prototype.isPrototypeOf(objectB)); // true
console.log(objectA.isPrototypeOf(objectB)); // true
```

当访问一个对象的属性时，对象的自有属性的优先级最高，若 JavaScript 引擎在对象的自有属性中没有找到目标属性，则会不断沿着原型链往上找，直到 `Object.prototype`

而 `Object.prototype` 的原型对象，实际上是个 `null` 而已

```js
console.log(Object.getPrototypeOf(Object.prototype)); // null
```

## 将原型对象设置为 null

`Object.prototype` 的 `[[Prototype]]` 便是 `null`

对于一个最简单的对象，其原型对象为 `Object.prototype`, 而 `Object.prototype` 中包含了若干个内置方法，如 `toString`, `valueOf()`

```js
console.log('valueOf' in Object.prototype); // true
console.log('toString' in Object.prototype); // true
```

而当将对象的原型对象设置为 `null` 后，这个对象中就再也不包含任何属性

```js
const plainObject = Object.create(null);

console.log('valueOf' in plainObject); // false
console.log('toString' in plainObject); // false
```

此时，这个 plainObject 是一个完美的哈希容器，因为在初始状态下，里面是真的空的，没有其他预定义的属性干扰



