# Redux 中间件原理

Redux 中间件的实现主要涉及到的函数

- applyMiddleware
- compose

## Redux 的作用

- 提供分类处理 action 的机会
- 在中间件中，可以检阅每一个调用的 action, 并选出特定的 action 进行相应的操作
- 中间件在每个 action 分发前调用

## applyMiddleware 源码

[applyMiddleware.js](https://github.com/reduxjs/redux/blob/6949ba0ce1d25aca9fa40d250d81c7e09beff30f/src/applyMiddleware.js#L19)

```js
import compose from './compose'

export default function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    const store = createStore(...args)
    let dispatch = () => {
      throw new Error(
        `Dispatching while constructing your middleware is not allowed. ` +
          `Other middleware would not be applied to this dispatch.`
      )
    }

    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args)
    }
    const chain = middlewares.map(middleware => middleware(middlewareAPI))
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
    }
  }
}
```

在理解这段代码前，首先明确几点：

- `compose` 函数是用来将多个中间件串联起来，这里先理解这一点
- 将中间件应用到每个 dispatch 前调用，其实现的原理是在原生 dispatch 上添加中间件的执行逻辑，再将这个加工后的 dispatch 替换原生 dispatch
    - 这里原生 dispatch 指的是没有应用中间件，最原始的 dispatch

---

首先，对于最外层的代码

```js
function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    const store = createStore(...args)
    
    // ...
    
    return {
      ...store,
      dispatch
    }
  }
}
```

`applyMiddleware` 函数接受了一个中间件方法的数组，返回的是一个函数。

这个返回的函数，实质上是创建 store 的一个函数。但 Redux 原本已经提供了一个 `createStore` 的函数来创建 store. 这是怎么回事呢？这里后面再解答[1]

但我们可以发现，返回的函数也接受一个 createStore 的参数，实际上，这个形参在函数执行时，传入的就是 Redux 提供的 `createStore` 函数。

所以，我们暂时只需要知道，`applyMiddleware` 返回的函数，其功能也是创建一个 store, 但这个 store 的创建方法又是建立在 Redux 本身的 `createStore` 之上，可以说是一个再加工

可以看到返回函数执行的第一行代码，就是创建了一个 store

```js
const store = createStore(...args)
```

而这个函数的返回值，就是通过 `...` 运算符，返回了一个 store, 并覆盖了 dispatch 的方法，store 的其他属性原封不动返回，符合上文说的 「替换原生 dispatch」

```js
return {
  ...store,
  dispatch
}
```

---

然后，再看到返回函数中的执行逻辑

```js
const middlewareAPI = {
  getState: store.getState,
  dispatch: (...args) => dispatch(...args)
}
```

可以看到，这里创建了一个 `middlewareAPI` 的对象，其中包含了 `getState` 与 `dispatch`, 实际上是为每个中间件提供访问 store 和当前 dispatch 的能力

```js
const chain = middlewares.map(middleware => middleware(middlewareAPI))
```

在这里 middlewares 是我们定义的中间件数组，通过 `map` 方法，将 middlewareAPI 塞到到每个中间件，为中间件提供访问 store 与当前 dispatch 的能力，并生成了一个加强版的串联中间件

```js
dispatch = compose(...chain)(store.dispatch)
```

在这里，将生成一个新的 dispatch, 用于替换原生的 dispatch, 而这个新的 dispatch, 就已经封装好了中间件的执行逻辑

本人在读源码时，由于对作者命名的变量名用意不明，不能很顺利理解源码。而在理解后，按照自己的命名风格，大致就是

```js
function applyMiddleware(...middlewares) {
  const enhancedCreateStore = (createStore) => (...args) => {
    const store = createStore(...args);
    const nativeDispatch = store.dispatch;

    const storeAccessAbilityAPI = {
      getState: store.getState,
      dispatch: (...args) => nativeDispatch(...args),
    };
    const chainedMiddlewares = middlewares.map(ele => ele(storeAccessAbilityAPI));
    const newDispatch = compose(...chainedMiddlewares)(nativeDispatch);

    return {
      ...store,
      dispatch: newDispatch,
    };
  };
  
  return enhancedCreateStore;
}
```

至于为什么 `compose(...chainedMiddleware)(nativeDispatch)` 这个可以生成新的 dispatch, 后面再说[2]

## compose 源码

```js
export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```

---

可以看到，`compose` 也是一个返回函数的函数，而我们更多的是关注最后一个 `return`

```js
return funcs.reduce((a, b) => (...args) => a(b(...args)))
```

当两个及以上的中间件时，`compose` 就会执行这个 `return`. 而它做的事情，就是把多个中间件串联起来

而要理解中间件串联起来的原理，首先我们要了解一个中间件的写法

### 如何写一个中间件

```js
const AMiddleware = (store) => (next) => (action) => {
  // do something...
  next(action);
  // do other things, perhaps
};
```

对于一个中间件执行的过程，大致可以分为 3 个阶段

1. (store) => {...} 这个阶段是在 `applyMiddleware` 函数中完成的，返回的是一个接受 `next` 参数的函数，而 store 就是存储了可以访问 `getState`, `dispatch` 这两个 API 的对象
2. (next) => {...} 这个阶段是在 `compose` 中完成的，返回的是一个接受 `action` 参数的函数，这个阶段在串联各个中间件
3. (action) => {...} 这个阶段是真正执行的时刻

---

现在了解了中间件的写法与大致的执行过程，就可以继续分析 `compose` 是如何串联中间件的。

实际上，串联操作就是，在 `compose` 中的 reduce 过程中，后一个中间件都是作为了前一个中间件的 `next`, 即下一个中间件。由于 JavaScript 中闭包的特性，后一个中间件（本质上是一个函数对象），都会被其前面的中间件（本质上也是一个对象）捕捉住

---

同时，我们也应该注意到，在中间件中由于会调用 `next(action)`, 那么，就会有调用前后的时机出现，因此，可以想象到，中间件的完整执行周期，就像浏览器事件一样，分为捕捉阶段和冒泡阶段

假设依次有中间件 A, B, C，而原生的 dispatch 为 p, 那么整个执行流程则为

```
next 调用前     |   next 调用后
A -> B -> C -> p -> C -> B -> A
```

## 遗留问题

### [1] 但 Redux 原本已经提供了一个 `createStore` 的函数来创建 store. 这是怎么回事呢？

看到 [createStore](https://github.com/reduxjs/redux/blob/6949ba0ce1d25aca9fa40d250d81c7e09beff30f/src/createStore.js#L53) 的源码

```js
if (typeof enhancer !== 'undefined') {
  if (typeof enhancer !== 'function') {
    throw new Error('Expected the enhancer to be a function.')
  }

  return enhancer(createStore)(reducer, preloadedState)
}
```

这里的 enhancer 实际上是原来 createStore 的第三个参数，中间件以 enhancer 的形式添加到 store 中

当 enhancer 不为空时，便会将原来的 createStore 作为参数传入到 enhancer 中

而 `applyMiddleware` 中 `...arg` 其实也就是 `reducer` 与 `preloadedState` 原封不动地传进去的

### [2] 为什么 `compose(...chainedMiddleware)(nativeDispatch)` 这个可以生成新的 dispatch?

又前面可以得知，`compose` 函数的结果是返回一个函数，因此 `compose(...chainedMiddleware)(nativeDispatch)` 是一个函数调用，那这个函数调用的结果是什么？

在 `compose` 中我们把中间件串联起来，串联起来后，得到的 `chainedMiddlewares` 也只是一堆中间件而已，而原生的 dispatch 并不在其中

而在 `compose(...chainedMiddleware)(nativeDispatch)` 中，实际上，我们还是把原生的 dispatch 也看作为一个中间件，合并到中间件的队伍中，而这个原生的 dispatch 同时作为中间件执行的终点，于是把它看作为新的 dispatch

