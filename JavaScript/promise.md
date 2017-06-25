# ES6 中 Promise 的简单使用

Promise 是用来规范 JavaScript 回调的对象

在 ES6 中，创建一个 Promise 对象的方法

```js
const promise = new Promise((resolve, reject) => {
	// 异步处理
 	// 异步处理完成，调用 resolve 和 reject 方法，根据需要传入参数
});
```

例子

以下方法中，返回了一个 Promise 对象，用于回调的处理

```js
static startIOSBiometricsAuthentication = (touchIDDescriptions) => {
    return new Promise((resolve, reject) => {
      const touchIDManager = NativeModules.TouchIDManager;

      const callback = (error, events) => {
        if (error) {
          reject(events);
        } else {
          resolve();
        }
      };

      touchIDManager.evaluateBiometricsPolicyWithDescriptions(touchIDDescriptions, callback);
    });
};
```

使用 Promise 对象进行回调

从调用的函数中，捕捉返回的 Promise 对象

```js
const promise = FingerPrintUtil.startIOSBiometricsAuthentication(touchIDDescriptions);
```

然后分别对成功和失败的结果进行处理（即回调）

```js
promise
	.then((value) => {
		// ... 成功的处理
	})
	.catch((error) => {
		// ... 失败的处理
	});
```

## 基础知识

Promise 有 3 个状态

- Pending, 初始状态，既不是成功，也不是失败
- fulfilled, 操作成功
- rejected, 操作失败

实际上，Promise 的 `then` 方法包含了两个函数类型的参数：`onfulfilled` 和 `onrejected`，因此，无论是成功还是失败，其实都会调用到 `then`

```js
promise.then(onFulfilled, onRejected)
```

> 当 Pending -> fulfilled，调用 then 的 onfulfilled 方法
> 当 Pending -> rejected，调用 then 的 onrejected 方法


