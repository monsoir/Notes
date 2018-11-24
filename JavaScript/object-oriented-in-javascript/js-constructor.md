# 构造函数

构造函数是，使用 `new` 创建对象时会调用的函数

构造函数实际上与普通的函数没区别，只是约定上，会将构造函数的函数名字第一个字母大写，用于区分

## 检查对象的类型

```js
const anObject = {
  name: 'object',
};

console.log(anObject instanceof Object); // true
console.log(anObject.constructor === Object); // true
```

检查一个对象的类型，可以使用

- `instanceof` 操作符，推荐使用
- `constructor` 属性，每个对象都会有一个构造函数属性，指向创建它的构造函数

## new 操作符的作用

```js
function Person(name) {
  this.name = name;
  this.sayName = function() {
    console.log(this.name);
  };
}

const person = new Person('monsoir');
person.sayName(); // monsoir
```

- `new` 操作符在创建对象时，会自动创建构造函数中的 `this` 对象
    - 若没有使用操作符 `new` 来创建对象，那么，构造函数体中的 `this` 就是全局对象
- `new` 操作符会自动帮助返回一个对象，因此，构造函数一般是不需要显式返回一个对象
    - 也可以在构造函数中显式返回一个对象A, A 会被作为结果返回，而忽略构造函数本身创建的对象
    - 若返回的是原始类型B, 则 B 会被忽略，构造函数该返回什么就返回什么

## 在构造函数中定义属性特征

```js
function Person(name) {
  Object.defineProperty(this,  "name", {
    get: function() {
      return name;
    },
    
    set: function(value) {
      name = value;
    },
    
    enumerable: true,
    configurable: true,
  });
  
  this.sayName = function() {
    console.log(this.name);
  };
}
```



