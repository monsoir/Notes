# JavaScript 原型对象

在 JavaScript 中只有一种结构：对象

每个对象 object 都有一个私有属性 [[Prototype]](这只是一个符号，实现根据解析引擎而异), 这是指向 object 的原型对象 prototype, 当然，这个 prototype 对象也有自己的原型，一只向上追溯，直到是 null

anObject.[[Prototype]] 是 anObject 的原型，从 ES6 开始，可以使用 `Object.getPrototypeOf()` 或者 `Object.setPrototypeOf()` 进行访问，等同于其他的实现 `__proto__`

函数也有原型对象，即 `aFunc.prototype`, 不同于 anObject.[[Prototype]]
aFunc.prototype 是使用 new aFunc() 创建后的对象的实例的 [[Prototype]]

```js
var prototype1 = {
    payload: 'object prototype',
};

console.log(prototype1.prototype); // undefined, 普通对象没有 prototype 这个属性

var a = Object.create(prototype1);
// var a = {};
// a.__proto__ = prototype1; // 这里跟 create 方法一样，普通对象只有 __proto__ 对象
console.log('before modifiying: ' + a.payload); // object prototype

prototype1.payload = 'object prototype modified';
console.log('after modifiying: ' + a.payload); // object prototype modified

// ----------------------

function fprototype2() {
    this.payload = 'function prototype';
}

console.log(fprototype2.prototype); // fprototype2 {} 函数有 prototype 对象

var b = new fprototype2();
console.log(b.prototype); // undefined 普通对象没有 prototype 对象
console.log(b.__proto__); // fprototype2 {} 
console.log('before modifiying: ' + b.payload); // function prototype

fprototype2.payload = 'function prototype modified'; // 并不能改变其中的实例属性啊
console.log('after modifiying: ' + b.payload); // function prototype

console.log(fprototype2.payload); // function prototype modified

// -----------------------

function fprototype3() {
    this.payload = 'function prototype';
}

console.log(fprototype3.__proto__); // [Function]

var c = new fprototype3();
console.log(c.__proto__); // fprototype3 {}

var d = Object.create(c);
// var d = {};
// d.__proto__ = c;
console.log('before modifiying: ' + d.payload); // function prototype

c.payload = 'function prototype modified';
console.log('after modifiying: ' + d.payload); // function prototype modified
```

总结：

1. 普通对象没有 `prototype` 对象，只有 `__proto__` 对象，这还要看具体环境实现的属性名称
2. 函数对象有既有 `prototype` 也有 `__proto__` 对象
    - `prototype` 是当函数用作构造函数时，新建出来的实例的原型对象，具体打印出来，就是函数内部的各种属性和方法
    - `__proto__` 是函数本身的原型，也就是 `Function`

---

更新

`__proto__` 是每个 JavaScript 对象都拥有的属性，包括函数，值是对应的原型对象（一个实例）

`prototype` 只有函数才有的属性，创建函数时，自动添加 `prototype` 属性，同时，这个 `prototype` 对象的值也是一个对象，并包含了一个 `constructor` 方法属性。通过 `new` 进行调用时，就会调用这个 `constructor` 方法

