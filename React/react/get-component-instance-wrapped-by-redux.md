# 组件被 Redux 包装后，获取组件的引用 ref

React 版本：16.5.2
React-Redux 版本：5.0.7

使用 react-redux 时，如果要将全局状态注入到组件中，我们的写法一般是

```js
// ...

const mapStateToProps = state => ({
  msg: state.user.msg,
});

const reduxWrappedComponent = connect(
  mapStateToProps,
)(AComponent);
export default reduxWrappedComponent;
```

一般情况下，这是没有问题的，但当我们想在父组件应用这个组件，并在某个时刻调用其一个方法，如 `getValues`

```js
this.theComponent.current.getValues();
```

此时，将会调用失败，`theComponent` 中并没有 `current` 这个属性

---

要想在父组件中通过 ref 调用被 redux 包装后的子组件的方法，需要在 `connect` 时传入适当的选项

在 `connect` 方法的定义文件中，不断回溯，可以找到下面的定义

```ts
export interface ConnectOptions {
    // ...
    /**
     * If true, stores a ref to the wrapped component instance and makes it available via getWrappedInstance() method.
     *
     * @default false
     */
    withRef?: boolean;
}
```

即添加 `{ withRef: true }` 选项，即可通过 `this.theComponet.getWrappedInstance()` 对组件进行引用并调用其方法

定义组件时

```js
// ...

const mapStateToProps = state => ({
  msg: state.user.msg,
});

const reduxWrappedComponent = connect(
  mapStateToProps,
  null,
  null,
  {
    withRef: true,
  },
)(AComponent);
export default reduxWrappedComponent;
```

挂载 ref 时

```jsx
<BasicInfoTab
  ref={(connectedComponent) => {
    if (connectedComponent) {
      this.theComponent = connectedComponent.getWrappedInstance();
    }
  }}
/>
```

通过 ref 调用组件方法时

```jsx
const values = this.theComponent.getValues();
```

⚠️ 注意点

- 与通常的方法不同，即组件没有被 redux 包装，调用 `getValues()` 时不需要通过 `current`, 即 `this.theComponent.current.getValues()`
- 在 react-redux v6 及以后，`withRef` 应使用 `forwardRef`

