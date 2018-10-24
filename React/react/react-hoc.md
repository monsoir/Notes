# React 使用高阶组件 HOC

在 React 中使用高阶组件，可以实现在较少干预的情况下，添加各种公用的功能

如现在有高阶组件 A, 在组件 B, C 中都需要用到同一种功能，除了在 B, C 中复制粘贴相同的代码片段外，还可以将通用的代码片段，在高阶组件 A 中实现，随后，B, C 都作为 A 的子组件，并在 A 中，将公用的功能方法，通过 `props` 的形式传递到组件 B, C 中。最后，在 B, C 中即可通过 `props` 取得公用方法，直接调用

例如 react-router 中的 `@withRouter`, 通过 `@withRouter`, 组件可以直接通过 `props` 获取到路由的信息

下面例子使用高阶组件，封装一个公用方法 `obj2QueryString`, 使得组件可以直接调用

`obj2QueryString` 方法的作用是，将 JavaScript 对象转换成 QueryString, 用于拼接到当前路由，保存当前页面的状态

---

`obj2QueryString` 的实现

```js
const obj2QueryString = (params) => {
  const queryString = Object.keys(params).map(key => (
    `${encodeURIComponent(key)}=${encodeURIComponent(params[key])}`
  )).join('&');
  return queryString;
};

export default obj2QueryString;
```

高阶组件 `WithQueryString` 的实现

```jsx
import React from 'react';
import obj2QueryString from '../../../utils/obj2QueryString';

function WithQueryString(BaseComponent) {
  return class extends React.Component {
    render() {
      return (
        <BaseComponent
          convertObj2QueryString={obj2QueryString}
          {...this.props}
        />
      );
    }
  };
}

export default WithQueryString;

```

- `BaseComponent` 就是将来会使用到 `obj2QueryString` 函数的组件，即上面提到的组件 B/C
- `WithQueryString` 的命名方式参考了 `WithRouter`
- `WithQueryString` 函数是一个返回组件类的函数，将 `obj2QueryString` 传入到 `BaseComponent` 后，还需要将调用者传递的 `props` 原封不动地继续传到 `BaseComponent`, 达到一种不动声色就能添加功能的效果

调用

```jsx
import React, { Component } from 'react';
import WithQueryString from 'path/to/WithQueryString';

class AComponent extends Component {
  componentDidMount() {
    const anObj = {
      a: 1,
      b: '123',
    };
    
    console.log(this.props.convertObj2QueryString(params));
  }
}
```

