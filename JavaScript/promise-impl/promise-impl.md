# Promise 实现

这个 Promise 的实现使用的是 TypeScript 进行编码，利用 TypeScript 有类型约束的特点，能够更好地表达代码的思路

## 状态

根据 [Promises/A+](https://promisesaplus.com) Requirements 2.1，Promise 内部有三个状态，分别为

- `pending`
- `fulfilled`
- `rejected`

`pengding` 为初始状态，只能又 `pending` 变为 `fulfilled` 或者又 `pending` 变为 `rejected` 这两种状态转换，其他情况都不允许

当状态为 `fulfilled` 时，Promise 中必须有一个字段（如 `value`），这个字段用于存储异步操作成功的结果；当状态为 `rejected` 时，Promise 中必须有一个字段（如 `reason`），这个字段用于存储异步操作失败的原因

> 实际上，都可以使用同一个字段，如 `value`, 来保存异步操作成功的结果，或异步操作失败的原因

因此，Promise 实际上是一个状态机，而这个状态机中包含三种状态，三种状态间只有两种合法的状态转换

因此，可以定义一个枚举来表示这三个状态

```ts
enum State {
  pending,
  fulfilled,
  rejected,
}
```

## Promise 结构大致骨架

```ts
class MyPromise<T> {
  // 1
  // 内部状态
  private state: State = State.pending;
  
  // 2
  // 异步操作结果，异步操作成功的结果或异步操作失败的原因
  private value: T | undefined | any;
  
  // 3
  // 异步操作失败的原因
  private reason: any;
  
  // 4
  // 用于保存异步操作成功或失败后的回调
  private handlers: PromiseHandler<T> = {
    onSuccess: [],
    onFailure: [],
  };
  
  // 5
  constructor(excutor: Excutor<T>) {
    try {
      executor(this.resolve, this.reject);
    } catch (e) {
      this.reject(e)
    }
  }
  
  // 6
  public then(onFulfilled?: FulfilledHandler<T>, onRejected?: RejectedHandler): MyPromise<T> {
  }
  
  // 7
  private resolve = (value: MyPromise<T> | T) => {
  }
  
  // 8
  private reject = (reason) => {
  }
}
```

1. [Promises/A+](https://promisesaplus.com) Requirements 2.1, Promise 需要包含内部状态
2. [Promises/A+](https://promisesaplus.com) Requirements 1.3, Promise 需要包含一个 value 值，这个值类型包括
    - 合法的 JavaScript 值
    - `undefined`
    - thenable
    - 一个 Promise 对象
3. [Promises/A+](https://promisesaplus.com) Requirements 1.5, Promise 需要包含一个 reason 来描述失败的原因
4. [Promises/A+](https://promisesaplus.com) Requirements 2.2.6, Promise 的 `then` 方法可以调用多次，当异步操作成功或失败后，需要根据对应的状态，按调用顺序一次回调。这里分别使用了 `onSuccess` 和 `onFailure` 两个数组保存注册的回调
5. 由于 Promise 实际上是一个「类」，因此这里使用类的形式定义一个 Promise 数据结构，在一个类里面，构造方法是少不了了
    - 在这里面，我们在构造的时候就已经开始执行传进来的方法，需要记住的是，在实际应用过程中，`executor` 的内容应该是耗时的操作内容，而不是平时写 Demo 那种 `console.log` 的瞬时操作
    - 同时，在 `executor` 中，我们把 `resolve` 和 `reject` 方法作为回调（又是回调）传进去，在 `executor` 完成后，将会根据相应的结果调用 `resolve` 或 `reject` 方法
    - 同时使用了 `try...catch` 的方法以防异常，一旦出现异常，想都不用想，就可以直接调用 `reject` 了
6. [Promises/A+](https://promisesaplus.com) Requirements 2.2
  - Promise 必须包含一个 `then` 方法，此方法用于暴露 value 和 reason
  - `then` 方法需要返回一个 Promise
  - `then` 方法需要接受两个参数，并且两个都是函数类型，当操作成功后，调用第一个参数；失败后调用第二个参数；而这两个函数参数，其实就是用来暴露 value 和 reason
7. 那问题就来了，根据 6 的描述，现在我们可以拿到 value 和 reason, 但是 Promise 又怎么知道异步操作是什么时候成功，什么时候失败呢？
    - 这就要取决于用户，在创建 Promise 的时候传入的 `executor` 中的 `resolve` 和 `reject` 的调用时机了，当调用 `resolve` 时，认为异步操作成功，调用 `reject` 则认为异步操作失败
    - 但创建 Promise 时候的 `executor` 中的 `resolve` 和 `reject` 都是由 Promise 内部暴露给用户
    - 因此 Promise 内部也需要两个分别处理成功和失败的方法，于是就有了内部的 `resolve` 和 `reject` 方法
8. 同 7

## 基础设施

由于使用了 TypeScript, 我们应该先利用好它的类型优势，先定义以下的类型，同时也使用了范性

### 类型定义

#### Executor

`Executor` 是创建 Promise 时传入给构造函数的参数类型

```ts
interface Executor<T> { (resolve: Resolve<T>, reject: Reject): void; }

constructor(executor: Executor<T>) {
  // ...
}
```

其中，`resolve` 和 `reject` 的类型也进行了定义

```ts
interface Resolve<T> { (value: T | MyPromise<T>): void; }
interface Reject { (reason: any): void }
```

#### PromiseHandler

`PromiseHandler` 是异步操作后的回调（包含成功和失败）

```ts
interface FulfilledHandler<T> { (value: T): any; }
interface RejectedHandler { (value: any): any; }

interface PromiseHandler<T> {
  onSuccess: FulfilledHandler<T>[];
  onFailure: RejectedHandler[];
};
```

#### Thenable

Thenable 实际上是一个含有 `then` 方法的对象，这里我们使用 `interface` 来进行约束

```ts
export interface Thenable {
  [key: string]: any;
  then: (...args) => void;
};
```

### 自省方法定义

#### 判读是否为函数类型

```ts
export const isFunction = (supposedFunction: any) => typeof supposedFunction === 'function';
```

## 一步步实现

### then

当在一个 Promise 对象 p 上调用执行 `then` 时，需要根据 p 的内部状态分情况进行调用，因为 `then` 方法可以在一个 p 上调用多次 👉 [Promises/A+](https://promisesaplus.com) Requirements 2.2.6

当 p 的 `state`

- 为 `State.pending` 时，将异步操作回调注册到对应的队列
- 为 `State.fulfilled` 时，判断成功回调操作是否为函数，如果是则将 value 回传，否则忽略
    - [Promises/A+](https://promisesaplus.com) Requirements 2.2.1.1
- 为 `State.rejected` 时，判断失败回调操作是否为函数，如果是则将 reason 回传，否则忽略
    - [Promises/A+](https://promisesaplus.com) Requirements 2.2.1.2

于是有：

```ts
public then(onFulfilled?: FulfilledHandler<T>, onRejected?: RejectedHandler): MyPromise<T> {
  const { state, value, reason } = this;
  
  switch (this.state) {
      case State.pending:
      {
        this.handlers.onSuccess.push(onFulfilled);
        this.handlers.onFailure.push(onRejected);
        break;
      }
      case State.fulfilled:
      {
        if (!isFunction(onFulfilled)) return;
        onFulfilled(value);
        break;
      }
      case State.rejected:
      {
        if (!isFunction(onRejected)) return;
        onRejected(reason);
        break;
      }
    }
}
```

但还有几个重要的条件 [Promises/A+](https://promisesaplus.com) Requirements 2.2.7:

1. `then` 方法需要返回一个 Promise 对象，即 `p2 = p1.then(fulfilledHandler, rejectHandler)`
- 当 `fulfilledHandler` 或 `rejectHandler` 抛出异常 `e` 时，p2 最终需要将 `e` 作为 reason 调用 p2 自己的 `rejectHandler`
- 当 `fulfilledHandler` 不是一个函数，但 p1 成功了，p2 就使用 p1 的 value 来调用 `fulfilledHandler`
- 当 `rejectHandler` 不是一个函数，且 p1 失败了，p2 必须将 p1 失败的 reason 作为自己的 reason 调用 p2 的 `rejectHandler`

上面的 2 - 4 点，简单来说，就是当 p1 的异步操作失败了，那么 p2 就直接失败；当 p1 的异步操作成功了，那 p2 就是用 p1 的异步结果作为输入继续 p2 自身的异步操作

但还有一个很重要的一点是  Promise Resolution Procedure, 当 `fulfilledHandler` 或 `rejectHandler` 返回一个值 result 时，result 可能是 `Thenable` 或者非 `Thenable`

a. 当 result 为 `Thenable` 时, 那么 p1 的异步结果将依赖 result 最后的异步结果，因此，result 异步操作完成后，p1 的异步操作才有可能完成
b. 当 result 不为 `Thenable` 时, 那么 p1 将带着 result 调用 `fulfilledHandler` 或 `rejectHandler`

因此，需要对上面的代码进行修改，解决第 1 个问题

```ts
public then(onFulfilled?: FulfilledHandler<T>, onRejected?: RejectedHandler): MyPromise<T> {
  const { state, value, reason } = this;
  // 记住，Promise 是需要两个函数作为入参
  // 这里返回的 Promise 相当于上面所说的 p2
  return new MyPromise<T>((onFulfilledNext, onRejectedNext) => {
    // ...
  });
}
```

---

再将 2 - 4 点作为一个问题解决，由于 p2 的执行依赖了 p1 的执行，因此会利用 JavaScript 闭包的特性，使得代码有点绕

```ts
public then(onFulfilled?: FulfilledHandler<T>, onRejected?: RejectedHandler): MyPromise<T> {
    // 1
    const { state, value, reason } = this;

    // 2
    const nextPromiseExecutor = (onFulFilledNext: Resolve<T>, onRejectedNext: Reject) => {
      const fulfill = (val) => {
        try {
          if (!isFunction(onFulfilled)) {
            // 上一环的 fulFill 不可执行，那就直接执行本环的
            // 这里，就体现着 Promise 链式调用时，获取到上一个环处理后的值的原因
            onFulFilledNext(val);
          } else {
            // 上一环的 fulFill 可执行，先执行
            // 并取得上一环的结果
            const result = onFulfilled!(val);
            if (result instanceof MyPromise) {
              // 但 result 还是一个 Promise
              // 需要先等待这个嵌套的 Promise 完成
              // 再执行本环的 fulFill 或 reject
              result.then(onFulFilledNext, onRejectedNext);
            } else {
              // result 只是一个普通值
              // 传给下一环作为入参
              // 这里，就体现着 Promise 链式调用时，获取到上一个环处理后的值的原因
              onFulFilledNext(result);
            }
          }
        } catch (e) {
          onRejectedNext(e);
        }
      };
      const reject = (reason) => {
        try {
          if (!isFunction(onRejected)) {
            onRejectedNext(reason);
          } else {
            const result = onRejected!(reason);
            if (result instanceof MyPromise) {
              result.then(onFulFilledNext, onRejectedNext);
            } else {
              onRejectedNext(reason);
            }
          }
        } catch (e) {
          onRejectedNext(e);
        }
      };
      switch (state) {
        case State.pending:
        {
          // then 方法可以多次调用，这里的多次调用指的是对同一个 promise 进行多次的 then 方法调用
          // 对于同一个 promise, 每一次调用 then 方法，就需要将成功或失败回调注册到 promise 上，以便之后按顺序进行回调
          this.handlers.onSuccess.push(fulfill);
          this.handlers.onFailure.push(reject);
          break;
        }
        case State.fulfilled:
        {
          setTimeout(() => {
            fulfill(value);
          }, 0);
          break;
        }
        case State.rejected:
        {
          setTimeout(() => {
            reject(reason);
          }, 0);
          break;
        }
      }
    };

    // 3
    return new MyPromise(nextPromiseExecutor);
  }
```

上面代码中的注释表明了代码的意图，其中 `fulFill` 和 `reject` 函数的思路是相同的，只是逻辑的目的有差异而已

这里，将代码分成了三部分，方便正好地整理思路

1. 获取当前 Promise 中的内部数据
2. 由于 `then` 方法需要返回一个 Promise 对象，而构建 Promise 对象需要一个 `Executor` 对象，于是，我们将 `Exeutor` 构建过程抽出来，这部分的逻辑比较长
3. 返回新的 Promise 对象

---

需要提到的一点是，注释中的「环」，实际上是指链式调用中的每一个 `then` 生成的 Promise, 如以下代码

```ts
promiseA.then((value) => { console.log(value); return value; }) // 环 a
        .then((value) => { console.log(value + 1); }); // 环 b
```

可以想象一下，每次调用 `then` 方法后，生成一个新的 Promise 对象，这些对象可以看成一个个的「环」，而这些环就形成了一条「链」

---

还需要注意到的是，在 `switch...case...` 中，`State.fulfill` 和 `State.reject` 状态下，使用了 `setTimeout` 强制异步

这是因为 [Promises/A+](https://promisesaplus.com) Requirements 3.1 中指出

> In practice, this requirement ensures that onFulfilled and onRejected execute asynchronously, after the event loop turn in which then is called, and with a fresh stack.

要知道的是，在 JavaScript 引擎中是有一个事件循环，每个事件循环有一定的执行时间。每一个事件循环结束之后，又会开启新的一个事件循环，不断等待接收外部的事件，或处理内部的事件。

假设调用 `then` 的时候是在事件循环 L1 中，那么在 L1 中就不可以执行 `fulfill` 或 `reject` 方法，而是要在 L1 结束后的另一个事件循环 L2 中进行调用，保证 `fulfill` 和 `reject` 是异步调用。而这里，可以通过 `setTimeout` 方法来进行实现，其中 `setTimeout` 又涉及了另一个[「宏任务」](../../js-task-invoke.md)的概念

### resolve

`resolve` 方法的主要任务是

- 更改 Promise 的状态
- 存储 value 值
- 执行之前在该 Promise 对象上注册的回调

```ts
private resolve = (value: T) => {
  this.state = State.fulfilled;
  this.value = value;
  this.executeRegisteredHandlers(this.handlers.onSuccess, this.value);
}
```

但又有一个问题是，如果传进来的 `value` 是一个 Promise 呢？如下面的调用

```ts
const promiseA = new MyPromise<number>((resolve, reject) => {
  resolve(new MyPromise((resolve2, reject2) => {
    resolve2(1);
  });
});
```

此时，`resolve` 中的形参 `value` 的值，实际上是 `new Promise((resolve2, reject2) => {resolve2(1)})`

对于这种情况，如果 `value` 是一个 Promise 类型的对象，那么，都是需要先将这个 Promise 解决掉之后，才能进行后面的调用

```ts
private resolve = (value: MyPromise<T> | T) => {
  // 必须为 pending 时才能执行，每个 Promise 的 resolve 只执行一次
  if (!this.isPending) return;

  // ⚠️: 有情况，由于 value 是任何类型的数据，所以 value 也可能是一个 promise
  // Aha, surprise!
  // 此时，(value as Promise) 的状态决定了当前 Promise 的状态

  if (value instanceof MyPromise) {
    const onFulFilled = (val) => {
      this.state = State.fulfilled;
      this.value = val;
      // 执行注册的回调
      this.executeRegisteredHandlers(this.handlers.onSuccess, this.value);
    };
    const onRejected = (reason) => {
      this.state = State.rejected;
      this.reason = reason;
      // 执行注册的回调
      this.executeRegisteredHandlers(this.handlers.onFailure, this.reason);
    };
    value.then((val) => { onFulFilled(val); }, (error) => { onRejected(error); });
  } else {
    this.state = State.fulfilled;
    this.value = value;
    // 执行注册的回调
    this.executeRegisteredHandlers(this.handlers.onSuccess, this.value);
  }
}
```

### reject

对于 `reject` 方法，它主要的任务是

- 更改 Promise 的状态
- 存储失败 reason
- 执行之前在该对象上注册的回调

```ts
private reject = (reason) => {
  // 必须为 pending 时才能执行，每个 Promise 的 reject 只执行一次
  if (!this.isPending) return;

  this.state = State.rejected;
  this.reason = reason;

  // 执行注册的回调
  this.executeRegisteredHandlers(this.handlers.onFailure, this.reason);
}
```

## Summary

一个具有基础功能的 Promise 大致就是围绕 `then`, `resolve`, `reject` 这三个方法来进行设计

上面代码的实现完整代码 [👉 promise-impl](https://github.com/pennyworthit/promise-impl.git)

## References

- [Promises/A+](https://promisesaplus.com)

