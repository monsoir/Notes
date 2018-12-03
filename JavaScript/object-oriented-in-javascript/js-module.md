# 模块模式

- 用来创建拥有私有数据的单例对象的模式，实际上也就可以构建一个创建对象的构造方法（类）
- 使用立即调用函数表达式(IIFE)进行创建

> 使用 IIFE 能够存储私有属性的原理
> 
> 函数体中的变量（私有变量）在函数体外并不能访问，如果需要访问这些私有变量，必须通过暴露给外界的方法或属性进行访问

使用模块模式的代码模版

```js
const aModule = (function() {

  // 定义私有属性的区域
  let _variable1 = '';
  let _variable2 = '';
  
  function getVariable1() {
    return this._variable1;
  }
  
  function getVariable2() {
    return this._variable2;
  }
  
  return {
    // 暴露外部可访问的方法或属性
    getVariable1,
    getVariable2,
  };
}());
```

- 最后，`aModule` 对象实际只包含了 `getVariable1` 与 `getVariable2` 两个方法的对象
- 就这样，外界并不能直接访问函数体中的 `_variable1` 与 `_variable2`, 要想访问这两个变量，就必须通过函数最后 return 出来对象中的 `getVariable1` 与 `getVariable2` 进行访问

