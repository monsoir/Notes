# React Router 简单使用

主要是浏览器端的 React Router 4 使用

## 安装

```sh
npm i -S react-router-dom
```

## 接入

`<BrowserRouter>` 需要包裹整个应用，若使用了 Redux, 则 `<Provider>` 包裹 `<BrowserRouter>`

```jsx
const ProviderApp = (
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>
);
```

## 各个组件

### `<Route>`

- 路由渲染组件
- 用法类似于一个自定义的控件

```jsx
<Route path='...' component={...}>blah</Route> // 贪心匹配

<Route path='...' exact component={...}>blah</Route> // 完全匹配，区别在于 exact

<Route path='/:args' component={...}>blah</Route> // 带参数
```

### `<Link>`

- 跳转用
- 约同于 `<a>`

```jsx
<Link to='...'>blah blah blah</Link>
```

> `<Link>` 中的 `to` 属性需要与 `<Route>` 中的 `path` 属性对应

### `<Redirect>`

- 专门用来跳转的组件
- `<Redirect to=‘…’></Redirect>` 可以放在最底部，用来匹配未能识别的路由，进行 404 的提示

### `<Switch>`

只渲染命中的第一个路由

```jsx
<Switch>
    <Route>…</Route>
    <Route>…</Route>
    <Route>…</Route>
</Switch>
```

## 组件含有的额外参数

使用 React-Router 时，组件的 `props` 会包含额外的与路由相关的参数

- `history`
    - `push()` 可以进行路由跳转
- `location` 包含页面信息
- `match` 路由参数相关，是否完全匹配，url
    - `params.location`

