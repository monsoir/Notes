# JavaScript 的继承机制

JavaScript 的继承机制成为原型对象链或原型对象继承

所有的对象，包括通过字面值创建的对象，都会自动继承 Object

## 继承自 Object.prototype 的方法

所有继承于 Object 的对象，都含有以下的方法

- `hasOwnProperty()` 检查自有属性
- `propertyIsEnumerable()` 检查自有属性是否可以枚举
- `isPrototypeOf()` 检查对象是否为另一个对象的原型对象
- `valueOf()` 返回对象的值表达
- `toString()` 返回对象的字符串表达

### valueOf()

每当操作符被用作一个对象时，就会调用对象的 `valueOf()` 方法

对于原始数据类型，是重写了 `valueOf()` 方法的，使得

- 对于 String 对象，返回一个字符串
- 对于 Boolean 对象，返回一个布尔值
- 对于 Number 对象，返回一个数字

另外，也使得

- 对于 `Date` 对象，返回一个数字类型的时间戳

因此，对于一个对象，若想在操作符下返回特定的值，则可以通过重写对象的 `valueOf()` 方法

### toString()

调用 `toString()` 方法的情况

- 当 `valueOf()` 返回的是一个引用，而不是原始值时，调用 `toString()`
- 当 JavaScript 期望得到一个字符串时，调用 `toString()`



