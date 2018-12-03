# 创建构造函数的私有属性

当我们创建 JavaScript 实例对象时，除了使用字面值进行创建外，也可以通过构造函数的方式进行创建，大致就是定义一个函数，随后配合使用 `new` 关键字进行创建

而上面的构造函数，就比较接近 Java 中类这种模版的概念，可以一次定义，随后创建的实例都包含某个特定字段的私有属性

这就是构造函数私有属性的含义，需要所有通过此构造函数创建的实例都可以共享到这个私有字段

## 实现

```js
const ObjectClass = (function(){
  let name = 'object'; // 每个新建的实例都共享这个私有字段
  
  function InnerObjectClass(name) {
    this.name = name;
  }
  
  InnerObjectClass.prototype.getSharedName = function() {
    return name;
  }
  
  InnerObjectClass.prototype.setSharedName = function(newValue) {
    name = newValue;
  }
  
  return InnerObjectClass;
}());

const object1 = new ObjectClass('Lannister');
const object2 = new ObjectClass('Stark');

// 一切都没改变前
console.log(object1.name); // Lannister
console.log(object2.name); // Stark
console.log(object1.getSharedName()); // object
console.log(object2.getSharedName()); // object

// 改变每个对象各自的属性
object1.name = 'Tyrion Lannister';
object2.name = 'Arya Stark';
console.log(object1.name); // Tyrion Lannister
console.log(object2.name); // Arya Stark
console.log(object1.getSharedName()); // object
console.log(object2.getSharedName()); // object

// 改变共享的私有字段
object1.setSharedName('Night King');
console.log(object1.name); // Tyrion Lannister
console.log(object2.name); // Arya Stark
console.log(object1.getSharedName()); // Night King
console.log(object2.getSharedName()); // Night King
```

- 最开始定义的 `name` 是多个实例对象共享到的私有属性，并且，更新一次数据，就会影响到所有实例对象
- 而每个实例对象中的 `name`, 即 `this.name` 中的 `name`, 则是每个实例对象自己拥有的，更新数据只会影响自己
- `ObjectClass` 实例对象实际上是 `InnerObjectClass` 函数，一个可以新建出实例对象的函数
- `ObjectClass` 实例对象本身又包含了一个私有字段 `name`

