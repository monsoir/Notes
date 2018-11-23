# 属性枚举

- 属性枚举的意思是，可以使用 `for-in` 循环遍历对象上的属性
- 用户添加的属性默认都是可枚举的
- 可枚举的属性，其内部特征 `[[Enumerable]]` 都是设置为 `true`

遍历对象属性时，使用 `for-in` 循环会遍历对象的自有属性和原形属性，而 `Object.keys()` 则只会遍历自有属性

可以使用 `propertyIsEnumerable()` 方法来检查一个属性是否可枚举

```js
const anObject = {
  name: 'object',
};

const properties = Object.keys(anObject);

console.log("length" in properties); // true
console.log(properties.propertyIsEnumerable("length")); // false, 数组的 length 属性不可枚举
```



