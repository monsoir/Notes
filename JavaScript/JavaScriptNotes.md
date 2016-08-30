# 节点
1. 元素节点
2. 文本节点
3. 属性节点

# 获取元素 通过 document 访问
1. 通过元素 ID -> `getElementById` 返回一个对象
2. 通过标签名字 -> `getElementsByTagName("<tag>")` 返回一个数组
3. 通过类名 -> `getElementsByClassName("<class1> <class2>")` 返回一个数组
	- 多个类名使用空格分开

# 获取、设置属性 通过 元素节点 访问
- setAttribute("<attribute>", "<value>")
	- 此方法作出的修改不会反映在源代码的文档，修改后，源代码还是修改前的值
- getAttribute("<attribute>")
	- 没有则返回 `null`

# 事件处理函数

> 给元素添加事件处理函数后，一旦事件发生，相应的 JavaScript 代码得到执行。被调用的 JavaScript 代码可以返回一个值，这个值将被传递给相应事件处理函数。（指示是否执行默认的操作）

# childNodes 属性
- 获取任何一个元素的所有子元素

# nodeType 属性 
- 每一个节点都有 `nodeType` 属性
- 共有12种取值，其中有使用价值的
	- 元素节点.nodeType = 1
	- 属性节点.nodeType = 2
	- 文本节点.nodeType = 3
- 可以利用此属性对特定节点进行处理

# nodeValue 属性 
- 用来得到／设置一个节点的值
- `node.nodeValue`
- 然而，如获取 `<p>` 中的文本
	- `[<p>].nodeValue` 将返回空值，`<p>` 元素本身的 `nodeValue` 为空值
	- `<p>` 元素中的文本是一个文本节点，所以应通过其子节点的第一个节点去获取

		```js
		[<p>].childNodes[0].nodeValue;
		```


