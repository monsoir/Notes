# React 组件生命周期

官方推荐 Cheat sheet

![react life cycle cheat sheet](https://ws3.sinaimg.cn/large/006tNbRwgy1fufi6paixhj31kw0v0dmn.jpg)

> v16.4.2

## Mounting 阶段

组件实例被创建和插入到 DOM 中


- `constructor()`
- `static getDerivedStateFromProps()`
- `render()`
- `componentDidMount()`

> `componentWillMount()` 被标记为 legacy, 不推荐再使用

## Updating 阶段

props 与 state 的改变都会引起 update

1. `static getDerivedStateFromProps()`
2. `shouldComponentUpdate(nextProps, nextState)`
3. `render()`
4. `getSnapshotBeforeUpdate()`
5. `componentDidUpdate()`

> `componentWillUpdate()`, `componentWillReceiveProps()` 被标记为 legacy, 不推荐再使用

## Unmounting 阶段

组件实例被移除出 DOM

- `componentWillUnmount()`

## Error Handling 阶段

异常处理方法，能捕抓到的异常范围有渲染过程，生命周期方法，子组件的 constructor 方法

- `componentDidCatch(error, info)`

## 很少用到的生命周期方法

- `shouldComponentUpdate(nextProps, nextState)`

## 不推荐再使用

- `componentWillMount()`
- `componentWillUpdate()`
- `componentWillReceiveProps()`


### `render()`

- 唯一必须实现的方法
- 返回值必须为如下其中一种
    - React 元素
    - Arrays | Fragments
    - Portals 将组件插入到其他父组件中，如 Modal 的实现
    - String | Number 视作字符节点
    - Boolean | null

> 当 `shouldComponentUpdate()` 返回 `false` 时，`render()` 不会执行

### `constructor(props)`

- 如果实现，则必须先执行 `super(props)`
- 什么时候用
    - 初始化 `state`
    - 绑定方法到实例
- `state` 值直接复制到 `this.state`
- 不要调用 `setState()`

### `componentDidMount()`

- 适合执行的操作
    - 需要 DOM 节点的数据
    - 请求数据
    - 建立监听

### `componentDidUpdate(prevProps, prevState, snapshot)`

- 更新发生后立即调用
- 初始渲染不会被调用
- 适合做的事
    - 需要使用的更新前后状态的比较结果来进行的接口调用
- 可以调用 `setState()` 方法，但需要有条件比较之后再调用，否则陷入死循环
- `snapshot` 参数只有在 实现了 `getSnapshotBeforeUpdate()` 方法后才会有值

> 当 `shouldComponentUpdate()` 返回 `false` 后，`componentDidUpdate` 不会被调用

### `componentWillUnmount()`

- 适合做的事，清理工作
    - 注销 timer
    - 取消网络请求
    - 卸载监听


### `shouldComponentUpdate(nextProps, nextState)`

- 在初始渲染和 `forceUpdate()` 调用时不会被调用
- 此方法只是用于性能优化的目的
- 相比手动覆盖此方法，更加推荐让组件继承 `PureComponent` 方法
- 返回 `false` 并不会阻止子组件的渲染行为
- 在此方法中，不推荐使用 `JSON.stringify()` 来比较前后的状态

### `static getDerivedStateFromProps(props, state)`

- 调用时机：初始渲染 + 后续更新
- 返回一个对象表示需要更新状态，返回 `null` 表示不需要更新状态
- 使用时机：state 需要依赖 props

> 关于是否应该使用此方法的指导 [You Probably Don't Need Derived State](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)

### `getSnapshotBeforeUpdate(prevProps, prevState)`

- 多用于获取 DOM 中的信息，由于 `setState` 有一定的延迟，在这里可以获取一些信息





