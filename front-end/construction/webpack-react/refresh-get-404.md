# 🔧 非一级页面刷新返回 404

> 情况
> 
> 1. 正常进入首页
> 2. 进入二级页面
> 3. 在二级页面中进行刷新操作
> 
> 结果：返回 404

经查看，发现二级页面中的资源请求 URL 是

```
http://localhost:8080/article/main.a502def00f686dbcd16a.js
http://localhost:8080/article/css/main.a502def00f686dbcd16a.css
```

正确的请求 URL 应是

```
http://localhost:8080/main.a502def00f686dbcd16a.js
http://localhost:8080/css/main.a502def00f686dbcd16a.css
```

现在需要做的是，将每个页面的资源请求定位到 `/`, 而不是将请求的资源名称拼接在页面路由上进行请求

---

## 方法一

在 `index.html` 中使用

```diff
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="theme-color" content="#000000">
+ <base href="/">
  <link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.3.3/semantic.min.css" />
  <title>Monsoir Space</title>
</head>
```

> HTML <base> 元素 指定用于一个文档中包含的所有相对URL的基本URL。一份中只能有一个<base>元素

## 方法二

其实是后来发现配有配置 Webpack 配置文件中的 `output.public`, 所以就配置起来吧

```js

const publicPath = '/';

output: {
  filename: 'static/js/[name].[hash].js',
  chunkFilename: 'static/js/[name].[hash].chunk.js',
  path: buildPath,
  publicPath,
},
```

