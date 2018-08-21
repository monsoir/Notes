# React 中的代码分割

## `import()`

最好的代码分割方式，但目前(2018.8.21)的 ECMAScript 标准并不支持 `import()`, 因此使用的话需要转译

然而，流行的脚手架或打包器都已经支持 `import()` 语法

- Webpack 遇到 `import()` 后，会自动开始代码分割
- Create React App 对 `import()` 实现了支持，也可以直接使用
- Next.js 对 `import()` 的支持也是开箱即用
- 自己动手配置 Webpack 的代码分割，参考 [Code Splitting](https://webpack.js.org/guides/code-splitting/)
- 使用 Babel 对代码进行转译时，需要使用 [babel-plugin-syntax-dynamic-import](https://yarnpkg.com/en/package/babel-plugin-syntax-dynamic-import)

```js
// 具体使用方法
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

## 使用 React Loadable

React Loadable 是一个第三方库，提供友好的 API 进行使用（提供一个占位 View 的参数，在加载组件时呈现）

同事也提供对服务端渲染的代码分割

```jsx
import Loadable from 'react-loadable';

const LoadableOtherComponent = Loadable({
  loader: () => import('./OtherComponent'),
  loading: () => <div>Loading...</div>,
});

const MyComponent = () => (
  <LoadableOtherComponent/>
);
```

## 基于路由的代码分割

使用 React Router 与 React Loadable 来实现代码分割

```jsx
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import Loadable from 'react-loadable';

const Loading = () => <div>Loading...</div>;

const Home = Loadable({
  loader: () => import('./routes/Home'),
  loading: Loading,
});

const About = Loadable({
  loader: () => import('./routes/About'),
  loading: Loading,
});

const App = () => (
  <Router>
    <Switch>
      <Route exact path="/" component={Home}/>
      <Route path="/about" component={About}/>
    </Switch>
  </Router>
);
```

## References

- [Code-Splitting](https://reactjs.org/docs/code-splitting.html)
