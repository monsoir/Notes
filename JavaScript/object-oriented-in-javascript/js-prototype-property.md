# [[Prototype]] 属性

每个对象实例都有一个内部属性 `[[Prototype]]`, 用于追踪其原型对象，这个属性也就是一个指向其原型对象的指针

## `[[Prototype]]` 与 `prototype` 

之前我们在很多地方看到过 `[[Prototype]]` 与 `prototype` 的介绍与使用，发现两者好像一样，又好像哪里不一样，实际上：

1. `[[Prototype]]` 是存在于实例对象中，指向原型对象，而访问此属性是需要通过其他方式进行
2. `prototype` 是存在于原型对象中，或者说构造函数中，此属性是被实例对象共享的，可以直接访问此属性
3. 对于原型对象 A, 由它创建的实例对象 a1, a2, ..., A 的 `prototype` 与 a[n] 的 `[[Prototype]]` 指向是相同的

访问实例对象的原型对象 `[[Prototype]]`，使用 `Object.getPrototypeOf()` 方法

判断某个对象是否为另一个对象的原型对象，使用方法 `isPrototypeOf()`

```js
function Person {}

const p = new Person();

console.log(Person.prototype); // Person
console.log(Object.getPrototypeOf(p)); // Person
console.log(Person.prototype === Object.getPrototypeOf(p)); // true, prototype 与 [[Prototype]] 指向相同

console.log(Person.prototype.isPrototypeOf(p)); // true
console.log(Object.prototype.isPrototypeOf(p)); // true
```

## 无法给对象的原型属性赋值

```js
// 创建一个空对象
const anObject = {};

// 调用的是原型对象上的 toString 方法
console.log(anObject.toString()); // [object Object]

// 动态添加设置一个 toString 方法
anObject.toString = () => {
	return "[Object Custom]";
};

console.log(anObject.toString()); // [Object Custom]

// 删除 toString 方法
delete anObject.toString;
console.log(anObject.toString()); // [object Object]

// 再删除 toString 方法
delete anObject.toString;
console.log(anObject.toString()); // [object Object]
```

- 在最后，当我们进行了两次删除 `toString` 操作后，再调用 `toString` 仍然有效，毫无疑问，调用的是原型对象上的 `toString` 方法
- 实际上，并无法给一个对象的原型属性赋值，即在动态设置 `toString` 方法时，其实是在实例对象上添加了一个 `toString` 方法，而当我们在第一次删除 `toString` 方法后，由于实例对象上没有 `toString` 方法，因此，会沿用原型对象中的 `toString` 方法


