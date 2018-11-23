# 禁止扩展

对象与属性类似，也有内部特征

对象的内部特征 `[[Extensible]]` 是一个布尔值，指明对象是否可以扩展，默认都为可扩展

- 当 `[[Extensible]]` 为 `true` 时，新的属性可以添加到对象上
- 当 `[[Extensible]]` 为 `false` 时，禁止新的属性添加到对象上

---

## 禁止对象扩展

使用 `Object.preventExtensions()` 方法

## 检测对象是否可以扩展

即检查 `[[Extensible]]` 的值，使用 `Object.isExtensible()` 方法

```js
const anObject = {
  name: 'object',
};

console.log(Object.isExtensible(anObject)); // true, 对象默认为可扩展

Object.preventExtensions(anObject);
console.log(Object.isExtensible(anObject)); // false, 设置了对象不可扩展
```


