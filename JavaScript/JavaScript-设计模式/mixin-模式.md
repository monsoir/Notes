# mixin 模式

减少代码中重复出现的功能

示例代码

```js
var _ = require('unsderscore'); // 1

// 共享的功能代码
var logger = (function() {
    var CustomerLogger = {
        log: function(message) {
            console.log(message);
        },
    };
    
    return CustomerLogger;
}()); // 2

// 定制的
var Server = (function(aLogger) {
    var CustomerServer = function() {
        this.init = function() {
            this.log('Initializing ...'); // 4
        };
    };
    
    _.extend(CustomerServer.prototype, aLogger);
    return CustomerServer;
}(logger)); // 3

// 调用
(new Server()).init(); // 5
```

1. 上面的 mixin 模式是通过 Underscore.js 中的 `extend` 方法进行实现，可以将源对象 `aLogger` 中的所有属性复制到目标对象 `CustomerLogger` 中
2. 这种定义了一个匿名函数，立即调用的模式叫 **立即调用的函数表达式(IIFE, Immediately Invoked Function Expression)**，在上面的例子中，`logger` 其实是一个叫 `CustomerLogger` 的对象，它包含了 `log` 的方法
3. Server 实质上是一个函数对象，即 `CustomerServer`, 并且，通过了 `extend` 方法，将 `aLogger` 中的全部属性全部复制到了 `CustomerServer` 的原型对象上，使得可以在 `CustomerServer`(也就是后来返回的 `Server` 上直接调用)
4. 通过了 `extend` 方法的绑定后，`CustomerServer` 的 `init` 方法，执行时，`this` 就拥有了 `aLogger` 中的 `log` 方法，因此可以直接调用 `this.log()`
5. 由于 Server 只是一个函数对象，因此，创建一个 Server 事例的时候，要把 Server 当作构造函数调用，即 `new Server()`, 得到的实例之后，才能调用 `init` 方法，这个实例，也就是 `CustomerServer` 的一个实例

> mixin 与继承有点相似，但是
> 
> 使用继承，只能影响到单个类层次，而使用 mixin, 则可以在多个对象之间共享功能

