# 对象封印

一个被封印的对象，是不可扩展，且其属性都是不可配置的，以为着

- 不能给对象添加新属性
- 不能删除属性
- 不能改变属性的类型（数据属性与访问器属性之间的切换）
- 只能读写对象属性的值

使用 `Object.seal()` 方法来封印对象，对象的 `[[Extensible]]` 及其属性的 `[[Configurable]]` 都会被置为 `false`

使用 `Object.isSealed()` 判断对象是否被封印

```js
const anObject = {
  name: 'object',
};

console.log(Object.isExtensible(anObject)); // true
console.log(Object.isSealed(anObject)); // false

// 开始封印
Object.seal(anObject);
console.log(Object.isExtensible(anObject)); // false
console.log(Object.isSealed(anObject)); // true

const descriptor = Object.getOwnPropertyDescriptor(anObject, "name");
console.log(descriptor.configurable); // false, 封印后的对象，属性不可配置

// 动态添加一个属性
anObject.sayName = function () {
  console.log(this.name);
};
console.log("sayName" in anObject); // false, 封印后无法动态添加新属性

// 删除一个属性
delete anObject.name
console.log("name" in anObject); // true, 封印后，对象的属性删不掉

// 读写属性值
anObject.name = "object2";
console.log(anObject.name); // object2, 封印后的对象可以读写属性值
```

> JavaScript 封印后的对象，与 C++, Java 的类创建出来的对象类似

