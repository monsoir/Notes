# Webpack 构建速度优化

## Loader

一个基本的 loader 配置

```js
module.exports = {
  module: {
    rules: [
      {
        test: /.js$'/,
        use: ['babel-loader?cacheDirectory'],
        include: path.resolve(__dirname, 'src'),
      }
    ],
  },
};
```

优化方向

- 精准描述 `test` 的需求，不添加多余的后缀名，提高正则表达式性能
- 对于 babel-loader, 可以设置转换结果缓存，开启 `cacheDirectory`
- 使用 `include` 来精准包含需要 load 的文件位置
- 使用 `exclude` 来精准排除不需要 load 的文件

## resolve.modules

- 用于配置告知 Webpack 在哪些目录中寻找第三方模块
- 默认值是 `['node_moduels']`
    - 当前文件夹没找到的话，继续往上一层目录的同名文件夹中找，即 `../node_moduels`
    - 一般来说，第三方模块都是直接装在项目根目录 `./node_modules` 中

优化方向

- 直接指定项目根目录的 `./node_modules`, 减少回溯寻找时间

```js
module.exports = {
  resolve: {
    modules: [path.resolve(__dirname, 'node_modules')],
  },
}
```

## resolve.mainFields

- 用于配置第三方模块使用哪个入口文件
- 部分第三方模块可以用于多个环境，如浏览器，Node

对于第三方库，在它们的 package.json 中就有指明对于不同平台的入口文件

    {
    	"browser": "xxx.js",
      "main": "yyy.js",
    }

对于模块运行的平台 target 与 resolve.mainFields 的对应关系是

- web | webworker → `['browser', 'module', 'main']`
    - Webpack 会依次按照顺序寻找入口
- default → `['module', 'main']`

优化方向

- 直接设定入口文件，减少搜索步骤

```js
module.exports = {
  resolve: {
    mainFields: ['main'],
  }
}
```

## resolve.alias

- 通过别名将原导入路径映射成一个新的导入路径

部分第三方库中，可能包含了两套功能相同的代码，原因可能由于模块化的方法不同，已经编译好的压缩代码

优化方向

- 直接指定第三方库的代码路径

```js
module.exports = {
  resolve: {
    alias: {
      'react': path.resolve(__dirname, './node_modules/react/dist/react.min.js'),
    },
  },
}
```

这里直接指定了 React 代码的路径，此为生产环境中使用的代码

注意的是，此优化方法适用与整体性较强的代码，即模块中的代码，少了任何一部分都会导致不正常工作。对于工具类库，如 lodash, 当中包含了许多工具类函数，这些函数之间的关联性实际上并不强，就不合适使用此优化方法

## resolve.extensions

- 此选项维护一个后缀名列表，当导入的模块没有带文件名后缀时，Webpack 会使用此后缀名列表进行拼接，尝试询问模块文件是否存在
- 当列表长度越长，而正确的后缀名在列表中的为止越靠后，就会造成尝试的次数增加，进而影响构建性能

优化方向

- 后缀名列表尽可能小
- 出现频率高的后缀名往前放
- 对于后缀名出现频率小的，就不要放这里了，直接导入的时候写上后缀名，如 `.json`

## module.noParse

使 Webpack 忽略没有采用模块化的文件的解析处理，提高构建性能

优化方向

- 将没有使用模块化构建的第三方库，添加到 `noParse` 中忽略解析，如 JQuery

```js
module.exports = {
  module: {
    noParse: [Regex1, Regex2, ...],
  },
}
```

注意

- 被忽略的文件中，不能包含 `import`, `require`, `define` 等模块化语句


