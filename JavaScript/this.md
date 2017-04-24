# this

[from](https://segmentfault.com/a/1190000009166127)

- [全局环境下的 this](#全局环境下的this)
- [对象中的 this](#对象中的this)
- [构造函数中的 this](#构造函数中的this)
- [apply, call](#applycall)
- [bind](#bind)
- [闭包中的 this](#闭包中的this)
- [箭头函数中的 this](#箭头函数中的this)
- [绑定运算符](#绑定运算符)

## 全局环境下的this

在全局作用域中调用 this, this 的引用的就是 window

严格模式下，this 为 `undefined`，否则，会默认为 `window`

## 对象中的 this

### 函数在对象中定义

```js
var o = {
	color: "blue",
	sayColor: function() {
		return this.color; // 在对象中，故 this 指向对象 o
	},
};

o.sayColor(); // blue
```

### 函数在对象外调用，但通过对象调用

```js
var o = { color: "blue" };

function sayColor() {
	return this.color;
}

o.sayColor = sayColor; // 已经将外面（全局）的 sayColor 中的 this 绑定到对象 o 中
o.sayColor(); // blue
```

## 构造函数中的 this

```js
function person() {
	this.name = "Jack";
}

var o = new person();
console.log(o.name);
```

关键字 `new` 会调用构造函数，这里也就是调用了函数的构造函数，新建了一个函数对象，同时，this 也指向了这个函数对象

## apply, call

apply 和 call 用途都是在特定的作用域中调用函数，即设置函数体内的 this

### apply

接受两个参数

- 函数运行时的作用域
- 参数数组（其中一个）
	- Array 实例
	- arguments 对象

```js
function sum(num1, num2) {
	return num1 + num2;
}

function callSum1(num1, num2) {
	return sum.apply(this, arguments);
}

function callSum2(num1, num2) {
	return sum.apply(this, [num1, num2]);
}

console.log(callSum1(10, 10));
console.log(callSum2(10, 10));
```

在 callSum1 中，传入了 this 这个外部的作用域，`arguments` 为函数内部的一个参数数组

在 callSum2 中，传入了 this 这个外部的作用域，显式地将参数包装成一个数组传入

### call

call 与 apply 基本相同，但第二个参数，需要将函数参数逐个列举出来

```js
function sum(num1, num2) {
	return num1 + num2;
}

function callSum1(num1, num2) {
	return call(this, num1, num2); // <<< 不同在这里
}

console.log(callSum(10, 10));
```

## bind

ES5 开始定义的方法

aMethod.bind(thisWillBoundTo)

bind 会创建一个函数实例并返回，其中，aMethod 中的 `this` 会绑定到参数 `thisWillBoundTo` 这个对象中

```js
window.color = "red";
var o = { color: "blue" };

function sayColor() {
	console.log(this.color);
}

var object = sayColor.bind(o); // sayColor 的 this 已经被绑定到 o 中
object.sayColor(); // blue
```

## 闭包中的 this

闭包中的 this, 是基于运行时函数的执行环境的对象，在全局函数中，this -> window, 作为对象的方法，this -> 那个对象

```js
var color = "red";
var o = {
	name: "blue", 
	sayColor: function() {
		return function() {
			return this.color;
		};
	},
};

console.log(o.sayColor()()); // red
```

在上面，o.sayColor() 返回一个函数对象，之后再来一个 ()，表示调用返回的函数，但此时，函数调用的时候，this 已经指向了全局

```js
var o = {
	color: "blue",
	sayColor: function() {
		var innerThis = this;
		return function() {
			return innerThis.color;
		};
	},
};
console.log(o.sayColor()()); // blue
```

在上面，o.sayColor() 返回一个函数对象，之后再来一个 ()，返回的这个函数实例中，函数体中提前将 this 保存为指向对象的 this

## 箭头函数中的 this

箭头函数中的 this，指向的，就是定义时所在的对象，而不是使用时的对象

## 绑定运算符

函数绑定运算符 `::`

thisWillBoundTo::aMethod; <==> aMethod.bind(thisWillBoundTo);

```js
foo::bar;
// 等同于
bar.bind(foo);
```


