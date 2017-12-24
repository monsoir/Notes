# React-Redux Basic

- [React Native 0.45 安装 Redux 注意](#react-native-0-45-安装-redux-注意)
- [使用 React-Redux 的一些概念简介](#使用-react-redux-的一些概念简介)
	- [Action 是干什么的](#action-是干什么的)
	- [Action 的结构](#action-的结构)
	- [Action Creator](#action-creator)
- [Store](#store)
	- [Store 本身提供的方法](#store-本身提供的方法)
- [一些方法简介](#一些方法简介)
	- [Redux 中的方法](#redux-中的方法)
		- [CombineReducers](#combinereducers)
		- [createStore](#createstore)
		- [compose](#compose)
	- [react-redux 中的方法](#react-redux-中的方法)
		- [connnet](#connnet)
		- [Provider 组件](#provider-组件)
- [附上一张大图](#使用-react-redux-过程中-各个部分的关系)
- [Reference](#reference)

## ReduxStarter 使用说明

[👉 Demo 地址](https://github.com/Monsoir/ReduxStarter)

ReduxStarter 是一个普通的计数 App, 来源于 Reference 中注明

ReduxStarter 含有 3 个分支：

- master 主分支，主要用来编写文档，运行结果是官方的 Demo
- withRedux 使用 Redux 进行管理的计数 App
- withoutRedux 没有使用 Redux 进行管理的计数 App

可以通过 `git checkout <分支名称>` 来切换不同的分支

首先需要进行 `npm install`，若在命令完成后，无法运行项目，则可能是出现了 `React Native 0.45 安装 Redux 时出现 react-native 的 cli 被删除的情况` 的问题，参见 [React Native 0.45 安装 Redux 注意](#react-native-0-45-安装-redux-注意) 解决

## React Native 0.45 安装 Redux 注意

React Native 0.45 安装 Redux 时出现 react-native 的 cli 被删除的情况

[👉 解决方法](https://stackoverflow.com/a/44476694/5211544)

1. 若有 package-lock.json 文件，先将文件移到项目文件夹外
2. `rm -rf node_modules && npm install` 重装 node_modules
3. `npm install -S redux` 安装 Redux
4. 移走 package-lock.json 文件
5. `npm install -S react-redux` 安装 react-redux

> Redux 中并不包含 React 的绑定库，需要额外安装

## 使用 React-Redux 的一些概念简介

### Actions

[👉 中文文档](http://cn.redux.js.org/docs/basics/Actions.html)

#### Action 是干什么的

Action 承载着数据，这些数据是 Store 用于更改 state 的唯一根据

Action 承载的数据的来源有：

1. 服务器相应
2. 用户输入，操作
3. 其他非 View 产生的数据

如何将 Action 传送到 Store?

**一般通过 Store 对象的方法 `dispatch()` 将 Action 传送到 Store**

#### Action 的结构

1. Action 的本质只是一个普通的 JavaScript 对象，但是与普通的 JavaScript 对象有所不同，因为我们协定：

	**Action 内必须有一个字符串类型的 type 字段，指定这个 Action 的名称**

	> 而这个 type，并没有什么很了不起的地方，最后的作用，就是在 `switch` 语句中，用来识别一下是什么动作而已，因此，命名的时候，保证是**唯一**就好了。而定义这个 Action 名字的地方，一般建议新建一个文件进行存放

2. Action 中除了定义 type 字段以外，还可以附加其他数据，而这些数据，可以在更新 state 时用上，如

	```js
	{
		type: An_Action_Name,
		index: 5,
	}
	```
	
	> 如果在 Action 中传递数据，我们应该尽量减少这个数据的大小，比如，有一大堆数据是以类似数组之类形式存在的，当我们操作其中一个元素，需要通过 Action 传递到 Store 时，传递该元素在数组中的下标会更好，而不是把整个元素的对象放到 Action 中传递

### Action Creator

Action 的创建者，其实这个创建者只是一个普通的函数，只是这个函数**返回了一个 Action 结构的对象**，因此，又称为 Action 创建函数

### Store

[👉 中文文档](http://cn.redux.js.org/docs/api/Store.html#dispatch)

Store 就是统一管理 state 的地方

在 React 中使用 Redux, Store 应该只有一个

#### Store 本身提供的方法

- getState() 获取 state
- dispatch(anAction) 更新 state
- subscribe(listener) 注册监听器
- 通过 subscribe(listener) 返回的函数，**注销**监听器

---

为什么说是 Store 本身提供的方法呢？

**因为在 React 中使用 Redux, react-redux 帮我们做了一些方法的封装，一般不会直接用 Store 提供的方法，而是用 react-redux 封装好的方法**

## 一些方法简介

### Redux 中的方法

#### CombineReducers

这是 Redux 中的一个辅助函数，当应用复杂的时候，我们会对 reducer 进行分拆，分拆成多个函数，使用 `CombineReducers` 可以将分拆后的多个 reducers 进行合并，合并成一个 reducer

```js
combineReducers({
    counter
});
```

上面的 combineReducers 是简写得很厉害的

---

全面的写法：

```js
combineReducers({
	reducer1: myReducer1,
	reducer2: myReducer2,
	...
})
```

首先，需要知道的是，到了最后 state 对象中的 reducers 结构是

```
{
	reducer1: ...,
	reducer2: ....,
	...
}
```

记住一个 reducer 实质上是一个函数而已

分拆后的某一个 reducer ，这个 reducer 可以有自己的名字，如上面的 myReducer1, myReducer2，在合并到 state 时，就可以将 reducer 重新命名为 reducer1, reducer2

---

在 ES6 中，可以进行进一步的简写，就是形如上面简写很厉害的那种

```js
combineReducers({
    reducer1,
    reducer2,
    ...
});
```

然而，这跟下面的写法是一样的，也就是没有重新命名

```js
combineReducers({
    reducer1: reducer1,
    reducer2: reducer2,
    ...
});
```

#### createStore

createStore 的函数定义

```js
export default function createStore(reducer, preloadedState, enhancer)
```

- preloadedState 是用来初始化 state 用的
- enhancer 类型是 function，用来增强 store，Redux 中使用 applyMiddleware 来增强 store

createStore 最后返回

```js
return {
   dispatch,
   subscribe,
   getState,
   replaceReducer,
    [$$observable]: observable
}
```

#### compose

从右到左组合多个函数，每个函数接收它后面的函数作为参数，然后返回一个函数

### react-redux 中的方法

#### connnet

[👉 中文文档](http://cn.redux.js.org/docs/react-redux/api.html)

连接 React 组件与 Redux 的 Store，只有通过这个方法，React 组件才能参与到对 state 的监听

函数定义

```js
connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])
```

一般来说，我们只会用到前两个参数

---

对于不用 Redux 的情况，我们一般写完组件就直接把组件这个 class 进行 export

对于使用 Redux 的情况，有所不同，需要 export 调用 `connect` 之后的组件 

```js
export default connect(mapStateToProps, mapDispatchToProps)(aReactComponent);
```

---

##### mapStateToProps

函数定义

```js
[mapStateToProps(state, [ownProps]): stateProps]
```

组件内部需要定义此方法，并将 state 中关心的数据，在方法里面提取出来

实现的方式如下

```js
const mapStateToProps = (state) => ({
    // 用 key: Value 的方式，将 state 中关心的数据映射为组件内的数据
});
```

---

##### mapDispatchToProps

函数定义

```js
[mapDispatchToProps(dispatch, [ownProps]): dispatchProps]
```

组件内部需要定义此方法，并将响应方法在定义在其中

```js
const mapDispatchToProps = (dispatch) => {
	return {
		aFunctionName: (aParam) => { dispatch(Action); }
	}
};
```

##### 注意

值得奇怪的一点是，如上面所说，`dispatch` 是 `store` 对象中的一个方法，但是使用的过程中，我们却可以直接使用 `this.props.dispatch(Actioin)` 的方法直接发布 Action，并没有看到 store 的出现，而原因如下：

> If you do not supply your own mapDispatchToProps function or object full of action creators, the default mapDispatchToProps implementation just injects dispatch into your component’s props.

如果 `connect` 的时候，没有将 `mapDispatchToProps` 传入到 `connect` 中作为参数，那么，dispatch 方法默认会注入到组件的 props 中，成为一个属性

---

经过实践，有两种方法发布 Action 的时候，并不需要调用 store

1. 直接组件中发布

	```js
	
	// 组件中定义动作的响应
	actionReset = () => {
	   this.props.dispatch(reset());
	};
	
	...
	
	// connect 时只传入 state 转 props 部分，不传入 Dispatch 
	export default connect(mapStateToProps)(Home);
	
	// 调用时，直接调用组件中的方法
	<TouchableOpacity style={styles.operation} onPress={this.actionReset}>
	```

2. 通过 props 进行发布

	```js
	// 组件中，将响应方法映射成 props
	const mapDispatchToProps = dispatch => {
	    return {
	        actionReset: () => { dispatch(reset()); },
	        ...
	    };
	};
	
	// connect 时传入 state 转 props，也传入 dispatch 转 props
	export default connect(mapStateToProps, mapDispatchToProps)(Home);
	
	// 调用时，通过 props 进行调用
	<TouchableOpacity style={styles.operation} onPress={this.props.actionReset}>
	```

#### Provider 组件

[👉 中文文档](http://cn.redux.js.org/docs/react-redux/api.html)

将根组件，如 `<App />` 嵌套在 `<Provider>` 组件中，使得所有的子孙组件都可以访问到 store

> 这一特性，是通过 reactjs 的 context 的 API 实现的，其中一个重要的方法是 `getChildContext`

## 使用 react-redux 过程中，各个部分的关系

![使用react-redux 过程中，各个部分的关系](https://ws3.sinaimg.cn/large/006tNc79gy1fha6loyxetj31kw0pen9e.jpg)

## Reference

[https://segmentfault.com/a/1190000008741380](https://segmentfault.com/a/1190000008741380)

