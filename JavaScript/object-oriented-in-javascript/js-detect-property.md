# 属性探测

## 使用 in 操作符或 hasOwnProperty 方法

### in 操作符

检查属性是否存在，不应该使用

```js
if (anObject.aProperty) {
  // do something...
}
```

以为在 `if` 判断中，所有非零值都会返回 true, 零值都返回 false, 不够严谨

---

应该使用 `in` 操作符

```js
console.log("anPropertyName" in anObject); // 普通属性
console.log("anFunctionPropertyName" in anObject); // 方法
```

`in` 操作符不会对属性的值有任何“偏见”（即零值）

### hasOwnProperty 方法

然而，`in` 操作符会检查对象的自有属性及其原型属性，若只需要检查对象的自有属性，则需要使用对象的 `hasOwnProperty` 方法

```js
const anObject = {
  name: 'object',
};

console.log("name" in anObject); // true
console.log(anObject.hasOwnProperty("name")); // true

console.log("toString" in anObject); // true
console.log(anObject.hasOwnProperty("toString")); // false
```

