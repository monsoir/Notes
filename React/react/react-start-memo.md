# React Start Memo

React 快速上手 Notes

## React.DOM

预定义好的 HTML 元素集合，React 自身对基本的 HTML 元素进行分装的集合

## ReactDOM

- 浏览器中渲染应用的一种途径，如方法：`ReactDOM.render()`
- `Object.keys(React.DOM)` 查看可用的属性列表

## className htmlFor style

class 和 for 是 JavaScript 的关键字，不能直接在 JavaScript 中使用，分别使用 className 和 htmlFor 代替

style 不能像在 HTML 中使用字符串进行赋值，需要使用 JavaScript 对象进行赋值

## React.createClass

创建新组件

```react
var newComponent = React.createClass({
	/* JavaScript 对象定义 */
});
```

唯一必须做的事：实现 `render()` 方法，返回一个 React 组件，不能只返回文本内容

## React.createFactory

工厂方法，创建多个实例

```react
var ComponentFactory = React.createFactory(newComponent);

ReactDOM.render(
	ComponentFactory(),
	document.getElementById("app")
);
```

## this.props

- 获取组件的属性，根据属性来进行渲染，所有属性挂载到这个对象上
- this.props 为只读属性


## propTypes

- 组件的一个属性，可以选择性实现
- 用于声明组件的 **属性列表** 及其 **类型**
- 好处：
	- 让组件的使用者清楚属性的类型
	- React 检查数据类型，控制台会给出相应的警告，不需要顾虑类型
- `Object.keys(React.ProTypes).join('\n)` 可以给出可用的属性类型


## getDefaultProps()

- 可选实现方法 
- 为属性提供默认值，避免对属性进行额外的检查（防御性编码）

```react
var NewComponet = React.createClass({
	propTypes: {
		firstName: React.PropTypes.string.isRequired,
		middleName: React.PropTypes.string,
	},
	
	getDefaultProps: function() {
		return {
			/* 直接将所有属性写在这里了 */
			firstName: "a",
			middleName: "b",
		};
	},
});
```

## state setState()

- React 利用 state 的概念，当 state (数据)发生变化时，React 自动重建界面
- 把 `this.state` 看作是只读属性
- 不要自行调用 `this.render()`，让 React 自行调用
- 通过 `setState()` 对 state 进行更新
	- `this.setState()` 通过队列机制进行批量修改
	- `this.setState()` 被调用时，React 调用 `render()` 方法更行界面

## getInitialState()

- 可选实现方法
- 保证合法地取得数据
- 在初始化时把当中的属性的值复制


## props 与 state

- this.props 为属性：给外部世界设置组件的机制 ---> 传递给类构造函数的参数
- this.state 为状态：负责组件内部数据的维护 ---> 包含着私有属性

## 安全地改变属性值

- 使用 componentWillReceiveProps() 方法
- 保持复杂组件的状态一致，避免打乱内部计数器，布尔型标记，事件监听器等

```react
componentWillReceiveProps: function(newProps) {
	this.setState({
		text: newProps.defaultValue,
	});
},
```

## 组件的生命周期方法

- componentWillUpdate()
	- 组件再次渲染，`render()` 调用前，组件的 props 或 state 发生改变时触发


- componentDidUpdate()
	- `render()` 执行完毕，更新的组件已同步到 DOM
	- 不会再初始化渲染时触发

- componentWillMount()
	- 新节点插入 DOM 结构 **前** 触发

- componentDidMount()
	- 新节点插入 DOM 结构 **后** 触发
	- 可以在这里访问刚加载好的 DOM 节点，获取组件元素的大小 

- componentWillUnmount()
	- 组件从 DOM 中移除时立刻出发


- shouldComponentUpdate(newProps, newState)
	- `componentWillUpdate()` 前触发
	- 返回 `true` 或 `false` 来指明是否调用 `render()`

## JSX

### 使用空格

多个空格会合并成一个

### 使用注释

- 使用 `{}` 包裹的内容属于 JavaScript
- 使用 `{/* */}` 进行多行注释

### 使用 HTML 实体

- 不能直接在 JSX 中使用 HTML 实体，需要使用 Unicode 版本编码代替
- 可进行常量定义
- [编码表](https://dev.w3.org/html5/html-author/charref)
- 防范 XSS 攻击

### 在 JSX 中返回多个节点

- 按道理说，JSX 只能返回单个顶层节点
- 解决：
	- method 1: 使用 `<div>` 包裹多个节点
	- method 2: 使用数组变量存储多个节点，并传入到 `<div>` 中。。。其实也就是 method 1

## 项目结构

![](https://ww3.sinaimg.cn/large/006tKfTcgy1fe1us9cox5j306x07f0sz.jpg)

- index.html 将静态资源关联起来
- /js
	- /js/source 使用 JSX 编写的脚本
		- 整个应用中通用的文件
		- components 与某个特定组件相关的文件
	- /js/build 源代码经过转译，浏览器可以运行的脚本
- css
	- 整个应用中通用的文件
	- components 与某个特定组件相关的文件
- scripts 构建过程用到的命令行脚本

/css /js /images 存放的东西都叫静态资源

### index.html

引入的内容

- 所有 CSS 文件打包生成的单个 bundle.css 文件
- 所有 JavaScript 文件打包生成的单个 bundle.js 文件，包括应用中的组件及其依赖库，如 React
- 放置应用渲染容器 `<div id="app">`

**注意：单一 css 和 js 当项目太大时，初始化加载会非常耗时，此时可以使用懒加载**

## 现代化的 JavaScript (模块化)

### 模块化

#### CommonJS 广泛接受的模块化方案

- 一个文件中编写了逻辑，可以导出(export) 一个或多个符号（对象，函数，单独的变量
- 通常约定，一个模块只导出一个内容(一个 React 组件)

```react
/* 
	模块依赖 React 来调用 React.createClass()
	React 没有定义为全局变量，需要导入后再使用(require)
*/
var React = require('react);
var Logo = React.createClass({/*...*/});

// 导出组件
module.exports = Logo;
```

#### ECMAScript 模块

ECMAScript 延续模块化的思想，与 `var ** = require('***')` 和 `module.exports = ***` 相对，使用 `import ** from '***';` 和 `export default ***`

#### 类

使用类前：

```js
var Logo = React.creatClass({/*...*/});
```

使用类后：

```js
class Logo extends React.Component {/*...*/}
```

React 类语法与 ECMAScript 2015 的区别

- 对象没有定义属性，只用函数／方法。若需要定义属性，则在构造函数中通过 this 定义
- 方法通过 functionName() {} 形式定义，不需要添加 `function` 关键字
- 方法之间不需要通过 逗号 分割

```js
class Logo extends React.Component {
	someMethod() {	
		/*...*/
	} // 这里不需要 逗号
	
	anotherFunction() { // 定义函数不需要 function 关键字
		/*...*/
	}
	
	render() {
		return /*...*/
	}
}
```

## 安装必备工具

### 让 index.html 成功运行在浏览器，需要

- 创建 bundle.css
	- 此文件只是简单拼接 CSS，不需要依赖安装其他工具
- 让代码在浏览器中可读
	- 使用 Babel 转译
- 创建 bundle.js
	- 使用 Browserify 完成此项工作

### Browserify 的任务

- 拼接脚本文件
- 解析并引入所有的依赖
	- 告诉它 app.js 的位置，它会找出所有的依赖
- 引入一个 CommonJS 的实现
	- 保证 require() 调用可以在浏览器中正常工作
	- Babel 会把所有 import 语句转化为 require() 函数调用

### 安装 Babel 和 Browserify

通过 Node.js 附带的 npm(node package manager) 进行安装

#### 安装 Browserify

```cmd
# 安装命令
npm install --global browserify

# 验证安装是否成功
browserify --version
```

#### 安装 Babel

```cmd
# 安装命令
npm install --global babel-cli

# 检验安装是否成功
babel --version
```

#### Tip

上述命令中的 --global 选项，可以方便我们在全局范围通过命令来进行访问，但通常情况下，推荐在项目本地安装 Node 包（即不要 --global 选项）


### React 相关

安装 React 相关的依赖包

- react
- react-dom，独立于 React 的
- babel-preset-react，让 Babel 支持 JSX 以及其他 React 语法
- babel-preset-es2015，提供对新版本 JavaScript 特性的支持

安装命令

```cmd
# 进入到项目的目录，进行的是本地安装，位于项目的 node_modules

npm install --save-dev react
npm install --save-dev react-dom
npm install --save-dev babel-preset-react
npm install --save-dev babel-preset-es2015
```

## 构建项目

### 转译 JavaScript -- 让代码在浏览器中可读

```cmd
# 通过 Babel 转译 JavaScript

babel --presets react,es2015 js/source -d js/build

# 从 js/source 中读取所有文件，并转译其中的 React 和 es2015 语法
# 结果复制到 js/build 中
```

### 打包 JavaScript -- 创建 bundle.js

```cmd
browserify js/build/app.js -o bundle.js

# 应用入口为 js/build/app.js，找出其中所有依赖，并把结果输出到文件 bundle.js
```

### 打包 CSS -- 创建 bundle.css

将所有 CSS 文件拼接成一个

```shell
cat css/*/* css/*.css | sed 's/..\/..\/images/images/g' > bundle.css
```

### 在开发过程中构建

每次修改文件后，自动转换，打包 js，打包 css

#### 将构建过程中使用到的命令放到 /scripts/build.sh 中

```shell
# 转换 js
babel --presets react,es2015 js/source -d js/build

# 打包 js
browserify js/build/app.js -o bundle.js

# 打包 css
cat css/*/* css/*.css | sed 's/..\/..\/images/images/g' > bundle.css

# 完成
date; echo;
```

#### 安装 watch 的 npm 包

运行 watch 命令对 /js/source/ 和 /css 目录中的任意更改进行监听，一旦文件内容发生变化，就运行 scripts/build.sh 中的脚本
	
```shell
# 安装 watch
npm install --save-dev watch

# 开始监听目录
watch "sh scripts/build.sh" js/source css

# 开始监听目录（将上面的命令放到了 scripts/build.sh 中）
sh scripts/watch.sh
```

** 然而，经实践发现：运行 watch 时，提示无法找到 watch 命令**

因此改为用 Homebrew 下载，运行方法同上

```shell
brew install watch
```

## 发布

上线前的额外处理

- 代码压缩
- 图像优化
- 等等

JavaScript 压缩工具 -- uglify

CSS 压缩工具 -- cssshrink

发布前处理完毕，复制文件到内容分发网络(content delivery network, CDN)

### 发布脚本

```sh
# 删除上一个版本
rm -rf __deployme
mkdir __deployme

# 构建
sh scripts/build.sh

# 压缩 JavaScript
uglify -s bundle.js -o __deployme/bundle.js

# 压缩 CSS
cssshrink bundle.css > __deployme/bundle.css

# 复制 HTML 和图片
cp index.html __deployme/index.html
cp -r images/ __deployme/images/

# 完成
date; echo;
```

脚本运行完后，得到一个新目录名为 __deployme，包含的内容

- index.html
- 压缩后的 bundle.css
- 压缩后的 bundle.js
- images/文件夹

把整个目录上传到服务器，就可以发版本了

### 更加专业的构建工具

- Grunt
- Gulp


