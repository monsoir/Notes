# 对象冻结

冻结对象是封印对象更进一步的操作

冻结的对象

- 不能添加属性
- 不能删除属性
- 不能改变属性类型
- 不能写入属性值
- 对属性值只能做读操作

使用 `Object.freeze()` 来冻结对象，使用 `Object.isFrozen()` 判读对象是否被冻结

```js
const anObject = {
  name: 'object',
};

console.log(Object.isExtensible(anObject)); // true
console.log(Object.isSealed(anObject)); // false
console.log(Object.isFrozen(anObject)); // false

// 开始冻结
Object.freeze(anObject);
console.log(Object.isExtensible(anObject)); // false
console.log(Object.isSealed(anObject)); // true
console.log(Object.isFrozen(anObject)); // true

// 对属性写入新的值
anObject.name = 'object2';
console.log(anObject.name); // object, 冻结的对象无法对属性进行写操作
```

> 一般来说，冻结对象的操作很少会被用到，冻结的对象只会用来做某个时间点的快照

