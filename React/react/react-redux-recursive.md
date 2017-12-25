# 在使用 Redux 的组件中，递归调用自己

✘

```jsx
class AComponentInvokeItselfRecursively extends Component {
  render() {
    /* 递归调用在这里发生 */
    
    {
        return <AComponentInvokeItselfRecursively />
    }
    
    return <something />;
  }
}

const mapDispatchToProps = {
  anActionCreator,
};

export default connect(null, mapDispatchToProps)(AComponentInvokeItselfRecursively);
```

对于上述情况

1. 在递归调用的时候，我们使用的是 `AComponentInvokeItselfRecursively`
2. 而能够结合 Redux 使用的组件是 `const temp = connect(null, mapDispatchToProps)(AComponentInvokeItselfRecursively)` 的这个 temp

因此，当组件中触发了 `anActionCreator` 时，很可能会出现 `anActionCreator` 是 `undefined` 的错误。因为调用方是还没有融合 Redux 的组件

> `mapStateToProps` 同理

✔︎

```jsx
class AComponentInvokeItselfRecursively extends Component {
  render() {
    /* 递归调用在这里发生 */
    
    {
        // 这里调用的是已经结合 Redux 的组件
        return <ComponentWithRedux />;
    }
    
    return <something />;
  }
}

const mapDispatchToProps = {
  anActionCreator,
};

const ComponentWithRedux = connect(null, mapDispatchToProps)(AComponentInvokeItselfRecursively);
export default ComponentWithRedux;
```

## References

- [Rendering Recursive Components in React-Redux](https://howtoember.wordpress.com/2016/05/18/rendering-recursive-components-in-react-redux/)

