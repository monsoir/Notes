# 属性类型

- 数据属性
- 访问器属性

## 数据属性

包含一个值，`[[Put]]` 方法默认行为是创建数据属性，因此，大多数情况下，我们使用到的都是数据属性

## 访问器属性

一句概括，就是 `getter` 和 `setter`

```js
const anObject = {
  _name: 'object',
  
  
  get name() {
    // do something when reading value
    return this._name;
  },
  
  set name(value) {
    // do something when setting value
    this._name = value;
  }
};
```

- 使用下划线定义属性，只是约定的方式
- 定义 `getter` 和 `setter` 时，不需要 `function` 关键字

