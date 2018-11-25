# 原型对象

几乎所有函数都有一个 `prototype` 的属性，此属性是一个原型对象用来创建新的对象实例用的，所有由同一个原型对象创建出来的实例对象，都共享一个原型对象

> 因此，可以把原型对象看作是对象的基类
> 但是原型对象并不是像 C++, Java 那种类，原型对象其实也是一个实例对象

由于原型对象被它的实例对象共享，因此，所有的实例对象都可以访问原型对象中的属性，如 `hasOwnProperty` 方法是被定义在 `Object` 这个原型对象中，因此所有 JavaScript 实例对象都可以调用 `hasOwnProperty` 方法

```js
const anObject = {
  name: 'object',
};

console.log("name" in anObject); // true, name 属性存在于 anObject 的自有属性中
console.log(anObject.hasOwnProperty("name")); // true, name 属性存在于 anObject 的自有属性中

console.log("hasOwnProperty" in anObject); // true, hasOwnProperty 方法存在与 anObject 原型对象 Object 中
console.log(anObject.hasOwnProperty("hasOwnProperty")); // false

console.log(anObject.prototype.hasOwnProperty("hasOwnProperty")); // true
```

## 鉴别原型属性

```js
function hasPrototypeProperty(object, name) {
  return name in object && !object.hasOwnProperty(name);
}
```

