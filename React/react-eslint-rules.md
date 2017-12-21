# 遵守 React 内置的 ESLint 规则

## jsx-a11y/click-events-have-key-events

> Visible, non-interactive elements with click handlers must have at least one keyboard listener. (jsx-a11y/click-events-have-key-events)

✘

```jsx
<img
  src={this.props.favico}
  alt={this.props.name}
  className="favicon"
  onClick={() => {}}
/>
```

✔︎

```jsx
<img
  src={this.props.favico}
  alt={this.props.name}
  className="favicon"
  onClick={() => {}}
  onKeyDown={this.handleClick}
/>
```

### Reference

[click-events-have-key-events](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/click-events-have-key-events.md)

## jsx-a11y/no-noninteractive-element-interactions

部分标签如 <img>, 不能包含交互效果，需要另外用一个具有交互效果的标签包裹

✘

```jsx
<img
  src={this.props.favico}
  alt={this.props.name}
  className="favicon"
  tabIndex={0}
  onMouseDown={this.handleClick}
/>
```

✔︎

```jsx
<div
  className="faviconHolder"
  role="button"
  tabIndex={0}
  onMouseDown={this.handleClick}
>
  <img
    src={this.props.favico}
    alt={this.props.name}
    className="favicon"
  />
</div>
```

## Reference

[no-noninteractive-element-interactions](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-noninteractive-element-interactions.md)



