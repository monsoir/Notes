# 样式的进一步处理

## 支持 CSS Modules

- 防止 CSS 全局污染的有效手段，通过对 css 属性名后添加 hash 编码
- `css-loader` 自带的功能，需要配置后才能使用

```diff
module: {
  rules: [
    ...
    {
      test: /\.css$/,
      // use: ['style-loader', 'css-loader'],
      use: [
        MiniCssExtractPlugin.loader,
-       'css-loader',
+       {
+         loader: 'css-loader',
+         options: {
+           modules: true,
+           localIdentName: '[local]__[hash:7]',
+       },
    }
  ],
},
```

- `modules` 设置为 `true`, 开启 CSS modules 功能
- `[local]__[hash:7]` 是 css 属性名字的格式，如，`content` 属性，处理后，属性名称变为 `.content__e60dec3`

使用时，我们可以将 CSS 文件

## 支持 SCSS

SCSS 使得 CSS 更具编程的能力，要使 Webpack 支持 SCSS, 需要使用到 loader 来将 SCSS 转换为 CSS

安装

```sh
npm i -D sass-loader node-sass
```

> 解析 SASS, 需要使用到 [sass-loader](https://github.com/webpack-contrib/sass-loader) 与 [node-sass](https://github.com/sass/node-sass)
> 实际上，配置的时候只会接触到 `sass-loader`, 并不会直接用到 `node-sass`

配置

```diff

...

module.exports = {
  ...
  module: {
    rules: [
      ...
+     {
+       test: /\.scss$/,
+       use: [
+         MiniCssExtractPlugin.loader,
+         {
+           loader: 'css-loader',
+           options: {
+             modules: true,
+             localIdentName: '[local]__[hash:7]',
+           },
+         },
+         'sass-loader',
+       ],
+     }
    ],
  },
  
  ...
  
```

- 这里处理 SASS 的步骤是
    1. 使用 `sass-loader` 将 SASS 转换为 CSS
    2. 使用 `css-loader` 处理 CSS 文件，并将 CSS 模块化
    3. 使用 `mini-css-extract-plugin` 将 CSS 文件从 JS 中抽离

## 使用 PostCSS

PostCSS 是另一个插件系统，最常用的功能是自动为 CSS 属性添加前缀

安装

```sh
npm i -D postcss postcss-loader
```

如果我们需要为 CSS 自动添加前缀，那就需要另外安装对应的插件，并应用到 PostCSS 中

```sh
npm i -D autoprefixer
```

配置

配置 PostCSS 配置文件，在项目根目录下创建 `postcss.config.js` 文件

```js
module.exports = () => ({
  plugins: {
    autoprefixer: { browsers: ['last 5 version', '>1%', 'ie >=8'] },
  }
});
```

上述配置是大致意义是

- 适配每种浏览器的上5个版本
- 适配全球市场份额大于 1% 的浏览器
- 适配版本大于 8 的 IE 浏览器

> 更多的配置意义可见 [browserslist](https://github.com/browserslist/browserslist)
> 
> 实际上，以上的配置还可以写在 `package.json` 的 `browserslist` 属性中

配置 Webpack

```diff
...

module: {
  rules: [
    ...
    {
      test: /\.css$/,
      use: [
        'style-loader',
        {
          loader: 'css-loader',
          options: {
          modules: true,
            localIdentName: '[local]__[hash:7]',
          },
        },
+       'postcss-loader',
      ],
    },
    {
      test: /\.scss$/,
      use: [
        MiniCssExtractPlugin.loader,
        {
          loader: 'css-loader',
          options: {
            modules: true,
            localIdentName: '[local]__[hash:7]',
          },
        },
+       'postcss-loader',
        'sass-loader',
      ],
    }
  ],
},

...
```

`postcss-loader` 的添加位置，其实也是比较容易理解的，但首先需要记住一点：loader 的执行顺序是从数组的最后一项开始往第一项开始执行

- 对于 CSS, 我们可以直接使用 `css-loader` 进行处理，但是我们在处理前，需要对 CSS 属性添加浏览器前缀，即先使用 `postcss-loader` 对原始的 CSS 代码进行处理，再将处理后的 CSS 代码交给 `css-loader` 处理
- 对于 SASS, `css-loader` 不能直接处理，需要通过 `sass-loader` 进行转换。即 `sass-loader` 将 SASS 转换为无前缀的 CSS 代码，而后再用 `postcss-loader` 生产出有前缀的 CSS, 最后交给 `css-loader` 处理

修改一下 `Content2.scss` 文件的内容为

```scss
$fontColor: blue;

.content2 {
  // color: $fontColor;
  // display: inline;
  display: flex;
  height: 200px;
  align-items: center;
  justify-content: center;
  color: #8FBC8F;
}
```

> 记得修改 `Content.jsx` 中对应引入的代码

执行 `npm run build`, 可以看到 CSS 的生成结果是

```css
.content2__8ea4f3a {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  height: 200px;
  -webkit-box-align: center;
      -ms-flex-align: center;
          align-items: center;
  -webkit-box-pack: center;
      -ms-flex-pack: center;
          justify-content: center;
  color: #8FBC8F; }
```


