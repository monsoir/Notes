# 属性特征

ES5 开始提供的，可与属性内部特征进行交互的 API

## 通用特征

数据属性和访问器属性都具有的特征

- `[[Enumerable]]` 决定是否可以遍历该属性
- `[[Configurable]]` 决定是否可配置，`delete` 操作符只可以删除可配置属性

---

使用 `Object.defineProperty()` 方法来修改属性特征，属性接受3个参数

- 拥有该属性的对象
- 属性名
- 特征值对象

> 特征值对象中的 key, 使用 `enumerable` 来设置 `[[Enumerable]]` 特征，使用 `configurable` 来设置 `[[Configurable]]` 特征，以此类推

```js
const anObject = {
  name: 'object',
};

Object.defineProperty(anObject, "name", {
  enumerable: false,
});

console.log("name" in anObject); // true, anObject 是包含 name 属性
console.log(anObject.propertyIsEnumerable("name")); // false, 上面配置了 name 属性不可遍历

const properties = Object.keys(anObject);
console.log(properties.length); // 0, 由于上面配置了 name 属性不可遍历，因此 anObject 可遍历自有属性，没有！

Object.defineProperty(anObject, "name", {
  configurable: false,
});

delete anObject.name;
console.log("name" in anObject); // true, 由于配置了 name 属性不可配置，因此 delete 并不起作用

// 严格模式下，抛出异常，配置属性变为不可配置，是一个单向操作
Object.defineProperty(anObject, "name", {
  configurable: true,
});
```

## 数据属性特征

- `[[Value]]` 存放值的地方
- `[[Writable]]` 布尔类型，指示属性是否可写入

使用 `Object.defineProperty` 方法，也可以为对象创建一个原本不存在的属性

```js
const anObject = {};
Object.defineProperty(anObject, "name", {
  value: 'object',
  enumerable: true,
  configurable: true,
  writable: true,
});
```

使用 `Object.defineProperty` 为对象定义属性时，最好为所有特征值设置一个值，否则布尔型的特征，将会被设置为 `false`, 会导致属性不可遍历，不可配置，不可写入

## 访问器属性特征

- `[[Get]]` 即 getter
- `[[Set]]` 即 setter

使用例子

```js
const anObject = {
  _name: 'object',
};

Object.defineProperty(anObject, 'name', {
  get: function() {
    // do something when reading value
    return this._name;
  },
  set: function(value) {
    // do something when setting value
    this._name = value;
  },
  enumerable: true,
  configurable: true,
});
```

> 同时对属性设置数据属性特征和访问器属性特征，将会报错
> 试图读取一个没有 getter 的属性，只会返回 `undefined`


## 定义多个属性

`Object.defineProperty` 只能为对象定义一个属性，若需要为对象同时定义多个属性，则需要使用 `Object.defineProperties`

接受两个参数

- 被设置的对象
- 属性信息的对象，类似于哈希表，key 为属性名，value 为特征值

```js
const anObject = {};

Object.defineProperties(anObject, {
  // 如果是定义数据属性
  _name: {
    value: 'object',
    enumerable: true,
    configurable: true,
    writable: true,
  },
  
  // 如果是定义访问器属性
  name: {
    get: function() {
      // do something when reading value
      return this._name;
    },
    set: function(value) {
      // do something when setting value
      this._name = value;
    },
  },
});
```

## 获取属性特征

使用 `Object.getOwnPropertyDescriptor()`, 同时只能获取到自有属性，而返回的对象就是特征值，而特征值对象中的值，是根据属性是数据属性或访问器属性而定

```js
const anObject = {
  name: 'object',
};

const descriptor = Object.getOwnPropertyDescriptor(anObject, "name");

// descriptor.enumerable
// descriptor.configurable
// descriptor.writable
// descriptor.value
```



