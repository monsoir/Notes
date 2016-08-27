# Best Practice

1. 在视图中，通过对象的属性而非对象本身来进行引用绑定
2. 将控制器命名为 `[Name]Controller` 而不是 `[Name]Ctrl`


# Concepts

### DOM
- 文档对象模型(Document Object Model)

### HTML 结构
- HTML 文档
	- 纯文本文件，包含页面的结构以及由CSS定义的样式，或者可以操作样式的 JavaScript 代码
- HTML 节点
	-  嵌套在另一个元素內的元素或一串字符。除文本节点外，所有元素都是节点
- HTML 元素
	- 有一个开始标签和一个结束标签组成
- HTML 标签
	- 用来标记元素的开始和结束，本身用尖括号生命
- 属性
	- 给 HTML 元素添加额外的信息。使用形如 `key="value"` 的键值进行设置

### CSS
- 语法

	```css
	Selector {<Declaration>; <Declaration>;}
	<Declaration> = <property: value>
	```

- Selector
	- id
		- 为标有特定 id 的 HTML 元素指定特定的样式
		- 以 `#` 定义

		```css
		#para1
		{
			text-align:center;
			color:red;
		}
		```

	- class
		- 可在多个元素中使用
		- 以 `.` 定义

		```css
		.center {text-align:center;}
		```

### 设置==自定义==指令调用格式
- E:元素
- A:属性
- C:类
- M:注释

### HTML 中的布尔属性
- 布尔属性代表一个 `true` 或 `false` 值
- 当这个属性出现时，这个属性的值就是 `true`，无论实际值是什么；未出现时，就是 `false`


### Instructions

- `ng-href`
	- 等到插值（URL）生效时，再执行点击连接的行为
- `ng-src`
	- 在 `ng-src` 对应的表达式生效之前不要加载图像
- `ng-include`
	- 加载、编译并包含外部 HTML 片段到当前的应用中
- `ng-switch`
	- 和 `ng-switch-when` 和 `on="<property name>` 一起使用，
	- `ng-switch-on = <property name>` 根据 `<property name>` 的变化渲染不同指令到视图中
	- `ng-switch-default` 默认显示
	- `ng-switch-when=<Bingo value>` 值对上了
- `ng-view`
	- 设置将被路由管理和放置在 HTML 的视图的位置
- `ng-if`
	- 完全根据表达式的值，在 DOM 中==生成或移除==一个元素
	- `<expression> = false` 对应元素将从 DOM 中移除
	- `<expression> = true` 对应元素的一个克隆将被重新插入到 DOM 中
	- 与 `ng-show`、`ng-hide` 的区别
		- 不是通过 CSS 显示或隐藏 DOM 节点，而是真正生成或移除节点
	- 从 DOM 移除后，其==关联的作用域也被销毁==，重新添加到 DOM 后，通过原型继承从父作用域==生成一个新的作用域==
- `ng-repeat`
	- 遍历一个集合或为集合中的每个元素生成一个模版实例
	- 集合中的每个元素都会被赋予自己的模版和作用域
- `ng-class`
	- 动态添加 CSS
	- `ng-class="{even: $even, odd: $odd}"`
		- 使用对象作为参数时，格式为 `<key, value> = <样式名称, 条件>`
- `ng-init`
	- 设置内部作用域的初始状体
	- 削弱了代码结构的健壮性
- `ng-cloak`
	- 将内部元素隐藏，直到路由调用对应的页面才显示出来
	- 与 `ng-bind` 一样，可以避免未渲染元素闪烁
- `ng-bind-template`
	- 在视图中绑定多个表达式
	- `ng-bind` 一次只能绑定一个表达式
- `ng-change`
	- 表单输入发生变化时计算给定表达式的值
	- 需和 `ng-model` 联合使用
- `ng-from`
	- 在一个表单中嵌套另一个表单
	- 普通 HTML `<form>` 不允许嵌套表单
- `ng-select`
	- 将数据同 HTML 的 `<select>` 元素进行绑定
	- 与 `ng-model`、`ng-options` 一起使用

- 当 AngualrJS 编译 DOM 时会查找 `{{expression}}` 内的表达式，表达式会被自动注册到 `$watch` 服务中并更新到 `$digest` 循环中

### 指令中的子作用域
- `ng-app` 为应用创建 `$rootScope`
- `ng-controller` 以 `$rootScope` 或另一个 `ng-controller` 的作用域为原型创建新的子作用域
- `ng-app`
	 - 任何具有 `ng-app` 属性的 DOM 元素都被标记为 `$rootScope` 的起始点
	 - `$rootScope` 是作用域链的起始点，任何嵌套在 `ng-app` 的指令都会继承它
- `ng-controller`
	- 为嵌套在其中的指令创建一个子作用域，避免所有操作和模型绑定在 `$rootScope` 上
- 子控制器是复制而非引用父控制器的对象，但修改父控制器的对象会同时修改子控制器对象中的值，==反之不然==

> JavaScript 对象：
> 字符串、数字、布尔值是==值复制==
> 数组、对象、函数是==引用复制==

- 将模型对象的某个属性设置为字符串、函数，它会==通过引用进行共享==
	- 在子 `$scope` 中修改属性会修改父 `$scope` 的对应属性

### 指令详解

#### 定义指令方法 `directive()`
- 参数
	- name 字符串类型，指令的名字
	- factory_function 函数类型，返回一个对象，定义指令的全部行为
		- `$compile` 服务利用其返回的对象，在 DOM 调用指令时构造指令行为

			```angularjs     .directive('myDirective', function() {
     		
     		});
			```
			
- 定义指令时，可用的选项

	```angularjs
	.directive('myDirective', function() {         return {             restrict: String,             priority: Number,             terminal: Boolean,             template: String or Template Function:                 function(tElement, tAttrs) (...},             templateUrl: String,             replace: Boolean or String,             scope: Boolean or Object,			 transclude: Boolean,			 controller: String or function(scope, element, attrs, transclude, otherInjectables) { ... }, 			 controllerAs: String,			 require: String,			 link: function(scope, iElement, iAttrs) { ... },			 compile: // 返回一个对象或连接函数，如下所示                			 function(tElement, tAttrs, transclude) {							 return {			 pre: function(scope, iElement, iAttrs, controller) { ... }, 			 post: function(scope, iElement, iAttrs, controller) { ... }}// 或者							 return function postLink(...) { ... }
		}	}; 
});
	```

### 服务

##### 创建服务的方法

- `factory(name: String, getFn: Function)`
- `service(name: String, constructor: Function)`
- `provider(name: String, aProvider: Object/Function/Array)`
- `constant(name: String, value: Constant)`
- `value(name: String, value:value)`

