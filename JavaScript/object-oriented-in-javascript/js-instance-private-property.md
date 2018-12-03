# 创建对象的私有属性

这里「对象的私有属性」意思是

- JavaScript 中实际上所有东西都是一个对象实例，并没有真正的类（真正的类指的是像 Java 中的类），连所谓的类，其实也是一个对象实例
- 只能通过特定的方法去访问一些外部不可见的数据（私有属性）

实现实例对象的私有属性，使用的是模块模式，即使用 IIFE

## 实现

```js
const anObject = (function() {
  let name = 'object'; // 一个私有属性
  
  function getName() {
    return name;
  }
  
  function setName(newValue) {
    name = newValue;
  }
  
  return {
    getName: getName,
    setName: setName,
  };
}());

console.log(anObject.name); // undefined, 不能访问内部数据 name
console.log(anObject.getName()); // object, 通过对外暴露的方法访问内部数据 name

anObject.name = 'abc';
console.log(anObject.name); // abc, 这只是为 anObject 动态添加了一个属性 name, 并不能设置其内部数据 name
console.log(anObject.getName()); // object

anObject.setName('tcejbo'); // 通过对外暴露的方法，设置内部数据 name 的值
console.log(anObject.getName()); // tcejbo
```

最后，`anObject` 对象应该是

- 一个具有 `getName` 与 `setName` 两个方法的对象
- 一个具有内部数据 `name` 字段，但外部并不能直接访问的对象

