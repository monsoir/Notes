# Visual Studio Code 识别 Webpack 中的 resolve.alias

编写 React App 时，当我们配置了 Webpack 配置文件中的 `resove.alias` 后，发现在模块引入处并不能直接跳转到源代码，此时，我们需要配置一个额外的文件，使得 VSCode 获知如何跳转

我们需要配置一个 [jsconfig.json](https://code.visualstudio.com/docs/languages/jsconfig) 文件

```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "allowSyntheticDefaultImports": false,
    "jsx": "react",
    "baseUrl": ".",
    "paths": {
      "@src/*": ["src/*"],
      "@img/*": ["src/img/*"],
    }
  },
  "exclude": ["node_modules", "build"],
}
```

最重要的几点 

- `module`, 设置模块化的组织，使用 `commonjs` 可以让 vscode 自动跳转到 `index.js` 文件
- `jsx`, 对于识别 React 组件的源文件不可少
- `baseURL`, 起始路径
- `paths`, 配置在 Webpack 配置文件中重命名的路径
- `exclude`, 排除某些文件夹，可用于提升速度

> 实际上，配置完成后，不仅可以在模块引入处跳转到源代码，同时还可以在使用处由 VSCode 自动引入


