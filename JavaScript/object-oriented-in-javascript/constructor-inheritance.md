# 构造函数继承

## 构造函数的默认设置

当我们定义一个函数时，实际上 JavaScript 引擎还在背后做了其他事情，使得我们可以使用这个函数来作为构造函数

一般我们会定义一个函数，用于创建一个对象

```js
function AConstructor() {
  // do some initialization things...
}
```

实际上，JavaScript 引擎会这样做

```js
AContructor.prototype = Object.create(Object.prototype, {
  constructor: {
    configurable: true,
    enumerable: true,
    value: AConstructor,
    writable: true,
  },
});
```

以上代码意味着

- 构造函数 `AConstructor.prototype` 被设置为继承自 `Object.prototype` 的对象
- 使用 `AConstructor` 创建出来的对象，都是继承自 `Object.prototype`

## 通过改变 [[Prototype]] 属性改变继承关系

```js
// 定义一个矩形函数
function Rectangle(length, width) {
  this.length = length;
  this.width = width;
}

// 加上实例方法
Rectangle.prototype.getArea = function() {
  return this.length * this.width;
};

Rectangle.prototype.toString = function() {
  return `[Rectangle ${this.length} x ${this.width}]`;
};

// 定义一个正方形函数
function Square(size) {
  this.length = size;
  this.width = size;
}

// 改变原型对象
Square.prototype = new Rectangle(); // 1
Square.prototype.constructor = Square; // 2

Square.prototype.toString = function() {
  return `[Square ${this.length} x ${this.width}]`;
};

const rect = new Rectangle(5, 10);
const square = new Square(6);
```

以上代码的意思是，首先创建一个矩形的函数，具有长，宽两个属性，后续再创建一个正方形的函数。而正方形是矩形的一个特例，因此可以正方形继承于矩形，不过长，宽都相等

1. 通过对 `Square` 的 `prototype` 属性进行设置，可以改变继承关系，这样 Square 也拥有了 `getArea` 方法

    > 为什么要通过 `new` 操作符将一个 Rectangle 对象赋值到 prototype 中？
    > 如果将 Rectangle.prototype 赋值过去，不利于 2 中构造函数的重新复制，因为这样把 Rectangle 原来的构造函数也修改掉
    > 而重新 new 一个 Rectangle, 可以拷贝一份副本，修改构造函数不会影响到全局

2. 修正 Square 的构造函数

## 更加直观的方法修改继承关系

Rectangle 与 Square 的例子中，实际上，Square 只需要继承到 Rectangle 中的方法，而构造函数需要维持不变，那么，也可以用下面的方式进行

```js
// 定义一个 Square 函数
function Square(size) {
  this.length = size;
  this.width = size;
}

Square.prototype = Object.create(Rectangle.prototype, {
  constructor: {
    configurable: true,
    enumerable: true,
    value: Square, // 重新制定构造函数
    writable: true,
  },
});
```

