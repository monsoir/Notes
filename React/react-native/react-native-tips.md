# React Native tips

## 图片自适应大小

将需要自适应的 `width` 或 `height` 样式值设置为 `null`

```js
<Image
  style={{ flex: 1, width: null, height: 80 }}
  source={anImage}
/>
```

## 设置 View 阴影的关键属性

```js
shadowOpacity: 0.75,
shadowRadius: 5,
shadowColor: 'gray',
shadowOffset: { height: 0, width: 0 },
```


