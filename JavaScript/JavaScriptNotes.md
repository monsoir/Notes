# 节点
1. 元素节点
2. 文本节点
3. 属性节点

# 获取元素 通过 document 访问
1. 通过元素 ID -> `getElementById` 返回一个对象
2. 通过标签名字 -> `getElementsByTagName("<tag>")` 返回一个数组
3. 通过类名 -> `getElementsByClassName("<class1> <class2>")` 返回一个数组
	- 多个类名使用空格分开

# 获取、设置属性 通过 元素节点 访问
- setAttribute("<attribute>", "<value>")
	- 此方法作出的修改不会反映在源代码的文档，修改后，源代码还是修改前的值
- getAttribute("<attribute>")
	- 没有则返回 `null`

# 事件处理函数

> 给元素添加事件处理函数后，一旦事件发生，相应的 JavaScript 代码得到执行。被调用的 JavaScript 代码可以返回一个值，这个值将被传递给相应事件处理函数。（指示是否执行默认的操作）

# childNodes 属性
- 获取任何一个元素的所有子元素

# nodeType 属性 
- 每一个节点都有 `nodeType` 属性
- 共有12种取值，其中有使用价值的
	- 元素节点.nodeType = 1
	- 属性节点.nodeType = 2
	- 文本节点.nodeType = 3
- 可以利用此属性对特定节点进行处理

# nodeValue 属性 
- 用来得到／设置一个节点的值
- `node.nodeValue`
- 然而，如获取 `<p>` 中的文本
	- `[<p>].nodeValue` 将返回空值，`<p>` 元素本身的 `nodeValue` 为空值
	- `<p>` 元素中的文本是一个文本节点，所以应通过其子节点的第一个节点去获取

		```js
		[<p>].childNodes[0].nodeValue;
		```


# 作用域 （部分特性）
- 忽略块级作用域

```js
var a = function () {
	for (var i = 0; i < 5; i++) {
		console.log("aaa");
	}
	
	// for 语句中的循环计数量 i，在循环体外，仍可访问到 i==5
	console.log(i);
}

a();
```

- 作用域的生命周期，使用引用计数（存在循环引用的问题）与标记清除（已经都使用这个了）


# 引用类型

## 创建 Object 实例的方法

### 1. new

```js
var person = new Object();
person.name = "Monsoir";
person.age = 22;
```

### 2. 对象字面量

- 不会调用 Object 构造函数

```js
var person = {
	name: "Monsoir",
	age: 22
};
```

```js
var person = {
	"name": "Monsoir",
	"age": 29,
	5: true // 数值属性自动转换为字符串
};
```

# Array 类型

## 创建数组的基本方式

### Array 构造函数

```js
var colors = new Array();
var colors = new Array(20);
var colors = new Array("red", "blue", "green");

// 省略 new 
var colors = Array();
var colors = Array(20);
var colors = Array("red", "blue", "green");
```

### 字面量

```js
var colors = ["red", "blue", "green"];
var colors = [];
```

## 索引

- 通过索引访问数组中的对象
	- 小于 Array.length，返回对应的值
	- 大于等于 Array.length，自动增加到该索引的长度
	- 没有则返回 `undefined`
- 通过设置 Array.length 在数组末尾移除或添加项
	- 新添加的项，若没有值，则置为 `undefined`

## 检测对象是否为数组

```js
if (Array.isArray(value)) {
	// Do something if value is an array.
}
```

## 转换方法

### toLocaleString()
- 创建一个数组值的以**逗号**分隔的字符串
- 调用数组中每一项的 `toLocaleString()` 方法

### toString()
- 返回由数组中每个值的字符串形式拼接而成的一个已**逗号**分割的字符串
- 调用数组中每一项的 `toString()` 方法

### valueOf()
- 返回数组本身

## 栈方法

### push()
- 接受任意个参数，并将它们逐个添加到数组末尾
- 修改数组长度

### pop()
- 从数组末尾移除最后一项
- 修改数组长度

## 队列方法

### shift()
- 移除数组中的第一项，并返回该项
- 修改数组长度
- 结合 `push()`，模拟队列操作

### unshift()
- 在数组前端添加任意项，并返回新数组的长度
- 结合 `pop()`，模拟相反方向的队列操作

## 重排序方法
- 返回经排序后的数组

### reverse()
- 反转数组项的顺序

### sort()
- 升序排序，最小在前，最大在后
- **默认**调用每个数组项的 `toString()`，比较得到的字符串

```js
var values = [0, 1, 5, 10, 15];
values.sort();
alert(values); // 0, 1, 10, 15, 5
```

- 可以接收一个 比较函数 作为参数，来确定数组项的排序位置

```js
// Define a compare function
function compare(value1, value2) {
	if (value1 < value2) {
		return -1;
	} else if (value1 > value2) {
		return 1;
	} else {
		return 0;
	}
}

// Invoke the compare function
var values = [0, 1, 5, 10, 15];
values.sort(compare);
alert(values); // 0, 1, 5, 10, 15
```

## 操作方法

### concat()
- 创建当前数组的一个副本，将接收到参数添加到副本的末尾，返回新构建的数组
- 没有参数，复制当前数组并返回副本
- 参数为一个或多个数组，将数组每一项添加到副本，并返回副本

### slice()
- 基于当前数组中的一个或多个项，创建一个新数组
- 参数：下标1 -> 指定位置的开始位置，下标2 -> 指定位置的结束位置

```js
var colors = ["red", "green", "blue", "yellow", "purple"];
var colors2 = colors.slice(1); // green,blue,yellow,purple
var colors3 = colors.slice(1, 4); // green,blue,yellow
```

### splice()
- 删除
	- splice(startIndex, countOfItemsToDelete)
	
	```js
	// 删除数组前两项
	var resultArray = originArray.splice(0,2);
	```

- 插入
	- splice(startIndex, 0<countOfItemsToDelete>, ...<items to insert>)

	```js
	// 从数组的位置2开始插入字符串 "red" 和 "green"
	var resultArray - originArray.splice(2, 0, "red", "green")
	```

- 替换
	- splice(startIndex, countOfItemsToBeReplaced, ...<newItems>)

	```js
	// 删除数组位置2开始的一个项，并从位置2开始添加 "red", "green"
	var resultArray = originArray(2, 1, "red", "green");
	```


## 迭代方法

- 5个
	- every() 对数组中的每一项运行函数，若函数对 **每一项** 都返回 true，则返回 true
	- some() 对数组中的每一项运行函数，若函数对 **任一项** 返回 true，则返回 true
	- filter() 对数组中的每一项运行函数，返回一个数组，该数组由对函数响应为 true 的项构成
	- forEach() 对数组中的每一项运行函数，没有返回值
	- map() 对数组中的每一项运行函数，返回每次函数调用结果组成的数组
- 可传入两个参数
	1. 应用到每一项的函数
	2. 运行参数函数的作用域对象－－影响 `this` 的值，**可选**
- 参数函数的3个参数
	1. 数组项的值
	2. 该项在数组中的位置
	3. 数组对象本身
- 参数函数的返回值 **可能也可能不** 影响方法的返回值

```js
var number = [1,2,3,4,5,4,3,2,1];

var everyResult = numbers.every(function(item, index, array){
	return item > 2;
});
alert(everyResult); // false

var someResult = numbers.some(function(item, index, array){
	return item > 2;
});
alert(someResult); // true

var filterResult = numbers.filter(function(item, index, array){
	return item > 2;
});
alert(filterResult); // [3,4,5,4,3]

var mapResult = numbers.map(function(item, index, array){
	return item * 2;
});
alert(mapResult); // [2,4,6,8,10,8,6,4,2]
```

## 归并方法

- 归并数组的方法，迭代数组的所有项，然后构建一个最终返回值
	- reduce() 从数组第一项开始遍历
	- reduceRight() 从数组最后一项开始遍历
- 可传入2个参数
	- 在每一项上调用的函数
	- 作为归并基础的初始值，**可选**
- 参数函数的参数
	1. 前一个值
	2. 当前值
	3. 项的索引值
	4. 数组对象
- 参数函数返回的任何值都会作为第一个参数自动传给下一项
- 第一次迭代发生在数组的第二项上

```js
var values = [1,2,3,4,5];
var sum = values.reduce(function(prev, cur, index, array){
	return prev + cur;
});
alert(sum); // 15
```

# Function 类型

- 推荐使用赋值的方式定义函数
- 没有重载

## 内部属性

### arguments
- 类数组对象
- 包含传入函数中的所有参数
- 含有 `callee` 属性，这是一个指针，指向拥有这个 `arguments` 对象的函数

```js
function factorial(num) {
	if (num <= 1) {
		return 1;
	} else {
		return num * factorial(num - 1); // 函数的执行与函数名耦合了！
	}
}

function factorial(num) {
	if (num <= 1) {
		return 1;
	} else {
		return num * argument.callee(num - 1); // 使用 callee 解耦
	}
}
```

### this
- 函数执行的环境对象

```js
function sayColor() {
	alert(this.color);
}

sayColor(); // this 指向的环境为全局环境，即 window

var o = {color: "blue"};
o.sayColor = sayColor;
o.sayColor(); // this 指向 o 的环境
```

### caller

- 保存着调用当前函数的函数的引用
- 在全局作用域中调用当前函数，caller 为 null

```js
function outer() {
	inner();
}

function inner() {
	alert(arguments.callee.caller);
}

outer(); // 显示 outer() 函数的源代码
```


