# Redux 三大原则

- 单一数据源
    - 数据源会是一个庞大的 JavaScript 对象，用 combineReducers 来化解
- 状态只读
    - 没有 set 方法，而是通过触发 action 来对 state 进行迭代
- 状态修改全部由纯函数完成
    - 状态可预测

# Redux 核心 API

- createStore(reducers[, initialState])
    - 创建 store

## store 的 API

- getState()
    - 获取 store 中的状态
    - dispatch(action)
        - 分发 action，通过 action 改变状态
    - subscribe(listener)
        - store 发生变化时调用
    - replaceReducer(nextReducer)
        - 更新当前 store 的 reducer
        - 一般在开发时用

> subscribe 与 replaceReducer 一般是 Redux 与其他系统（如 React）做桥接时使用


# React-Redux

- 只是提供一个组件 `<Provider />` 和一个 API `connect()` 帮助 Redux 与 React 进行绑定
- `<Provider />` 接受一个 store 作为参数，作为整个应用的顶层组件
- `connect()` 提供在任意 React 组件中获取 store 中的数据的功能

# Redux 中间件

- 提供分类处理 action 的机会
- 在中间件中，可以检阅每一个调用的 action, 并选出特定的 action 进行相应的操作
- 中间件在每个 action 分发前调用




