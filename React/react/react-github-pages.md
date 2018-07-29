# 将 React 项目部署到 GitHub Pages

仅限于静态页面

方法参照 [create-react-app 的官方文档](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#github-pages)

- [配置 package.json](#配置-packagejson)
- [安装 gh-pages 并配置发布脚本](#安装-gh-pages-并配置发布脚本)
- [发布](#发布)
- [确认代码分支](#确认代码分支)
- [哦，对了](#哦对了)

> 必要条件：react-scripts 版本需在 0.2.0 或以上

> 假设已经使用过了 create-react-app 创建了 React 项目

## 配置 package.json

在 package.json 中添加 homepage 字段，与 dependencies 同级，如：

```json
"private": true,
"homepage": "https://xxx.github.io/theApp"
```

create-react-app 将使用 homepage 值来配置访问页面


## 安装 gh-pages 并配置发布脚本

安装 gh-pages

```shell
npm install -D gh-pages
```

在 package.json 中添加发布脚本

```json
// ...
"scripts": {
	// ...
	"predeploy": "npm run build",
	"deploy": "gh-pages -d build"
}
```

`predeploy` 将会在 `deploy` 运行前自动运行

## 发布

运行命令

```shell
npm run deploy
```

## 确认代码分支

在项目的 settings 的 GitHun Pages 设置部分中，确保源代码 Source 使用的是 gh-pages 分支

---

到这里，发布配置基本完成，还有其他可选项，如自定义域名。

## 哦，对了

发布命令完成之后，需要等待一阵子才能生效，立即访问 homepage，很有可能不会得到想要的结果

## 注意点

- 默认使用的是 origin 源，所以当 origin 不是 GitHub 地址时，需要更换一下

### 可能出现的错误

`A branch named 'gh-pages' already exists`

- 解决方法 [https://github.com/transitive-bullshit/react-modern-library-boilerplate/issues/15](https://github.com/transitive-bullshit/react-modern-library-boilerplate/issues/15)
- `rm -rf node_modules/gh-pages/.cache`

