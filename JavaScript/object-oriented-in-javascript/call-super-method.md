# 调用父类方法

在继承的关系中，有时候我们会通过调用父类的方法来避免编写重复的代码，这个时候，需要使用到 `call` 或 `apply` 方法来改变运行时的 `this`

## 调用父类的构造方法

定义父类

```js
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
```

定义子类

```js
function Square(size) {
  Rectangle.call(this, size, size); // this 为 Square
  
  // do other initialization things...
}

// 修改原型对象
Square.prototype = Object.create(Rectangle.prototype, {
  constructor: {
    configurable: true,
    enumerable: true,
    value: Square, // 修改构造方法
    writable: true,
  },
});

Square.prototype.toString = function() {
  return `[Square ${this.length} x ${this.width}]`;
};
```

## 调用父类的其他方法

以调用父类的 `toString()` 方法为例

定义父类

```js
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
```

定义子类

```js
function Square(size) {
  Rectangle.call(this, size, size); // this 为 Square
  
  // do other initialization things...
}

// 修改原型对象
Square.prototype = Object.create(Rectangle.prototype, {
  constructor: {
    configurable: true,
    enumerable: true,
    value: Square, // 修改构造方法
    writable: true,
  },
});

Square.prototype.toString = function() {
  const text = Rectangle.prototype.toString.call(this);
  return text.replace('Rectangle', 'Square');
};
```

## 总结

1. 无论是调用父类的构造方法还是普通的方法，都是使用 `call` 或 `apply` 来修改运行时的 `this`
2. 而调用父类方法时，一个比较蹩脚的地方是，需要显式写明父类（构造方法），而不是像 Java 一样，使用 `super`

