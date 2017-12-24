# React-Redux 异步使用

## 总体思路

1. 使用第三方依赖 redux-thunk
2. 开启中间件 thunk
3. 在同步 Action 中返回函数对象，并使用 dispatch 提交异步 Action

## 安装第三方依赖

```sh
npm i -S redux-thunk
```

## 开启中间件

在 `index.js` 中，创建 Store 时开启中间件

```js
import { createStore, applyMiddleware } from ‘redux’;
import { thunk } from ‘redux-thunk’;
const store = createStore(aReducer, applyMiddleware(thunk));
```

在 Reducer 中，创建一个同步方法，返回一个 Action

```js
export function asynFunction() {
    return (dispatch) => {
        setTimeout(() => {
            // 异步执行
            // aFunction 是另一个 Action Creator
            dispatch(aFunction());
        }, 2000)
    }
}
```

[Shared-Bookmarks: category.redux.js](https://github.com/Monsoir/Shared-Bookmarks) 中的应用

```js
export function fetchCategories(address) {
  return ((dispatch) => {
    dispatch(startFetchingBookmarks());
    fetch(addProtocolIfNeeded(address))
      .then((response) => {
        return response.json();
      })
      .then((json) => {
        dispatch(updateBookmarks(json));
      })
      .catch((e) => {
        dispatch(failFetchingBookmarks());
      });
  });
}
```

