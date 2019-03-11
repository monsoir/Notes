# JavaScript 原型对象

在 JavaScript 中，创建对象有两种普遍的方式

- 通过字面值直接创建

    ```js
    const literalObject = {};
    ```

- 通过模版创建

    ```js
    function Template = {};
    const templateObject = new Template();
    ```
    
实际上，上面所说的「模版」，一般在 Java 等语言中，是使用 `class` 来进行标记。

在 ES6 及以后，也可以使用 `class` 关键字来进行标记，但其实现的原理并没有变，在 JavaScript 中，「模版」其实就是一个普通的 JavaScript 函数，但由于在 JavaScript 中，函数是一等公民，这就使得函数并不是那么普通了

## 原型对象结构

![](https://ws2.sinaimg.cn/large/006tKfTcgy1g0yyzh94crj30mh09q0tr.jpg)

这是从 《JavaScript 高级程序设计》中截取的原型对象的图，结合这个图，我们可以来了解当我们创建一个函数时发生了什么事

- 当我们创建了 `Template` 函数时，引擎同时会为函数 `Template` 创建一个属性，名叫 `prototype`, 而这个属性指向的目标就是函数的原型对象
    - 简而言之，创建函数时，函数本身会再创建一个 `prototype` 属性，同时，引擎根据这个函数以及一定的规则，创建一个学名为「原型对象」的对象，并且 `prototype` 指向这个「原型对象」
- 在为函数创建「原型对象」，即 `Template.prototype` 时，`Template.prototype` 默认会有且只有一个属性 `constructor`, 学名就叫做「构造函数」。 而这个 `constructor`, 实际上是指回了 `Template` 这个函数，于是，形成一个闭环。由于「原型对象」也是一个对象，所以也会附带了 `Object` 中的属性
    - 附带的意思是，这些属性并不是「原型对象」的自身属性，而是通过原型链可以查出来的属性

---

然后，再了解下关于图中 `[[Prototype]]` 这个属性

- 实际上，每一个实例对象，其内部都含有一个属性 A, 而 A 指向的是构造函数的原型对象
- 而这个 A, ECMA-262 􏰀第 5 版中称它为 `[[Prototype]]`, 但是没有没有制定标准的访问形式
- 虽然 `[[Prototype]]` 没有标准的访问形式，但是主流的几个浏览器都实现了通过 `__proto__` 属性来进行访问，因此在浏览器上，打印一个对象时，一般都会看到 `__proto__` 这个属性

--- 

根据上述的理解，我们可以得出下面的结论

- `prototype` 属性，只会在函数对象中出现。因为 `prototype` 指向的对象必须有一个 `constructor` 对象，而这个 `constructor` 又指向了自己

    ```js
    // 根据这条结论，于是就会有
    
    literalObject.prototype // undefined 没有值
    
    templateObject.prototype // undefined 没有值
    
    Template.prototype // {constructor: ƒ} 有值
    
    Template.prototype.constructor === Template // true
    ```

- `[[Prototype]]` a.k.a `__proto__` 是所有对象都有

    ```js
    // 根据这条结论，于是有
    
    literalObject.__proto__ // {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
    
    templateObject.__proto__ // {constructor: ƒ}
    
    Template.__proto__ // ƒ () { [native code] }
    ```

- `[[Prototype]]` 是指向其构造函数的原型，即 `anObject.[[Prototype]] === anObject.constructor.prototype`

    ```js
    // 根据这条结论，于是有
    
    literalObject.constructor
    // ƒ Object() { [native code] }
    // literalObject 的构造函数为 Object
    literalObject.__proto__ === Object.prototype // true
    
    templateObject.constructor
    // ƒ Template() {}
    // templateObject 的构造函数为 Template
    templateObject.__proto__ === Template.prototype // true
    
    Template.constructor
    // ƒ Function() { [native code] }
    // Template 函数本身就是函数类型的一个实例对象，所以它也有构造函数
    Template.__proto__ === Function.prototype // true
    ```
---

其他的一些认识

- 原型对象中的属性（函数），可以看成是实例对象的方法表。即当我们通过某个实例对象调用一个（非自有的）方法时，引擎会从原型对象中查找这个方法（查表），如果没有找到的话，就会查找这个原型对象的原型对象（表上表），直到查不到为止
- 原型对象中的所有属性都是共享的，例如上图中的 `constructor`, `name`, `age` 等属性。共享的意思是：如果我通过 Person 创建了 person1, person2, 如果我直接更改原型对象中的值，那么 person1, person2 访问相应的属性时，是会访问到修改后的值
- 每个实例都可以有属于自己的属性，例如 person1 中也可以有 `name`, person2 中也可以有 `name`, 且 `Person.prototype.name`, `person1.name`, `person2.name` 之间相互独立，只有当 person1 或 person2 中不存在 `name` 属性时，才会去读取 `Person.prototype.name` 的值

## 继承

在 JavaScript 中，实现继承的机制与 Java 等语言不同。Java 等语言实现的继承是复制式的继承，即父类和子类中的数据其实是隔离的。而 JavaScript 中实现的继承是引用式的继承，即所谓的父类和子类中的部分数据是通过引用关系来处理的，少有不慎，就会导致数据的不一致

在 JavaScript 中实现继承的核心在于，通过修改对象的原型对象类来实现一条原型链

以下例子是来自 《JavaScript 高级程序设计》

### 定义一个父类

通过第一部分可以知道，在 JavaScript 中，所谓的类，其基本也只是一个函数而已

```js
function SuperType() {
  this.property = true;
}
```

- 这里定义了一个 `SuperType` 的类型
- 并且，当使用 `new` 关键字新建一个实例 A 时，A 会含有一个属性 `property`, 默认值为 `true`
- `this` 的意思就是新建出来的对象，绑定在了这个上下文中

---

```js
SuperType.prototype.getSuperValue = function () {
  return this.property;
};
```

- 这里为 SuperType 的原型添加了一个方法，为原型添加方法即以为着只有 `SuperType` 的实例对象可以访问这个方法
- 同时 `this` 这个上下文也是绑定在了调用方法的实例对象上

### 定义一个子类

```js
function SubType() {
  this.subProperty = false;
}

SubType.prototype = new SuperType();
```

- 这里，要通过两个步骤才能使得 `SubType` 继承 `SuperType`
- 第一步是定义了一个 `SubType` 函数，这里也可以称之为类了。根据第一部分的描述，创建 `SubType` 函数的同时，会创建 `SubType` 的原型对象 `SubType.prototype`, 现在，我又先把这个对象成为 A. 由于 `SubType.prototype` 这个属性，本质上只是一个引用，其引用值时可以变化的。这里，我是把 `SubType.prototype` 所指向的那个对象成为 A. 
- 还有，当使用 `new` 创建一个实例 B 时，B 会含有一个属性 `subProperty`, 默认值为 `false`
- 第二步，也是重要的一步，改写 `SubType` 的原型。新创建一个 `SuperType` 的实例对象 A1, 并将其赋值到 `SubType.prototype`. 此时，上面说的 A 就变为了 A1, 虽然 `SubType.prototype` 看起来还是那个 `SubType.prototype`, 但是其具体内容，指向已经变化了

---

```js
SubType.prototype.getSubValue = function () {
  return this.subProperty;
};
```

- 这里，为 `SubType` 的原型添加了一个方法

---

最后，再将代码打印出来

```js
function SuperType() {
	this.property = true;
}

SuperType.prototype.getSuperValue = function () {
	return this.property;
}

function SubType() {
	this.subProperty = false;
}

SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function () {
	return this.subProperty;
}

const superInstance = new SuperType();
const subInstance = new SubType();

console.log(superInstance.getSuperValue()); // true
console.log(superInstance.getSubValue()); // TypeError: ...

console.log(subInstance.getSuperValue()); // true
console.log(subInstance.getSubValue()); // false

console.log(SuperType.prototype); // SuperType { getSuperValue: [Function] }
console.log(SubType.prototype); // SuperType { property: true, getSubValue: [Function] }

console.log(superInstance.constructor); // [Function: SuperType]
console.log(subInstance.constructor); // [Function: SuperType]
```

可以看到，实现继承后

- `SubType` 的原型对象变为了 `SuperType` 的一个实例对象，原型对象还有 `property` 这个 `SuperType` 的实例属性
- 任何 `SuperType` 的实例对象中，其原型对象都会有一个 `constructor` 的函数，而这个 `constructor` 又是指向 `SuperType` 函数。实现继承后，`SubType` 的实例对象中原型对象指向了 `superInstance`, 而 `superInstance` 的原型对象又是指向 `SuperType` 的原型对象，因此，`SubType` 的实例对象的原型对象的 `constructor` 从此也就指向了 `SuperType` 函数

