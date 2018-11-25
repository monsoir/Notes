# 在构造函数上操作原型对象

## 原型对象上的属性和方法都是共享的

添加方法

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayName = function () {
  console.log(this.name);
}

const person1 = new Person('object');
const person2 = new Person('another object');

person1.sayName(); // object
person2.sayName(); // another object
```

- 当方法定义在原型对象上时，方法就不再是对象的自有属性，同时，方法 `sayName` 的代码会被所有新建的实例对象所共享
- 在 `sayName` 中，当遇到了 `this` 时，`this` 会被分别设置为 person1 或 person2

---

添加属性

```js
function Person(name) {
  this.name = name;
}

Person.prototype.favorites = [];

const person1 = new Person('object');
const person2 = new Person('another object');

console.log(person1.favorites); // [ 'a', 'b' ]
console.log(person2.favorites); // [ 'a', 'b' ]
console.log(Person.prototype.favorites); // [ 'a', 'b' ]

console.log(person1.favorites === person2.favorites); // true
console.log(person1.favorites === Person.prototype.favorites); // true
```

可以看到定义在原型对象上的属性（此处为 `favorites`），会被实例对象和原型对象所共享，操作的都是同一个对象

## 简洁地向原型对象添加属性或方法

向原型添加属性或方法，我们最容易想到的是用一下方式

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayName = function () {
  console.log(this.name);
}
```

但使用这种方式，会很繁琐，每次都需要键入 `Person.prototype`

实际上，我们可以创建一个对象，然后将这个对象替换到原型对象上

```js
function Person(name) {
  this.name = name;
}

Person.prototype = {
  sayName: function() {
    // do something...
  },
  
  toString: function() {
    return "...";
  },
};
```

但是这种方式有一个副作用，就是会改变构造函数的属性， 这是因为赋值过去的对象是一个对象字面值，而对象字面值的构造函数属性是 `Object`

```js
const p = new Person('aaa');

console.log(p instanceof Person); // true
console.log(p.constructor === Person); // false
console.log(p.constructor === Object); // true
```

要消除这个副作用，只需要在对象字面值中，指定 `constructor` 属性为 Person

```js
Person.prototype = {
	constructor: Person, // 指定构造函数属性
	
	sayName: function() {
		// do something...
	},
	
	toString: function() {
		return "...";
	},
};
```


