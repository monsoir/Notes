# 《JSON 必知必会》Note

- [JSON 正确格式](#json-正确格式)
- [JSON 文件](#json-文件)
- [JSON MIME 类型](#json-mime-类型)
- [JSON 的安全问题](#json-的安全问题)
	- [跨站请求伪造](#跨站请求伪造)
	- [阻止 CSRF](#阻止-csrf)
	- [跨站脚本攻击](#跨站脚本攻击)
	- [防止跨站脚本攻击](#防止跨站脚本攻击)
- [跨域资源共享](#跨域资源共享)
- [JSONS-P](#json-p)
- [JSON 验证工具](#json-验证工具)

## JSON 正确格式

- 重要的一点：
	- 键值对必须是使用双引号包围

	```json
	{
		"name": "no name",
		"age": 2
	}
	```
	
## JSON 文件

- JSON 可以作为独立的文件存在于文件系统中
- 文件扩展名为 `.json`

## JSON MIME 类型

- application/json
- [MIME 类型全部列表](https://www.iana.org/assignments/media-types/media-types.xhtml)

## JSON 的安全问题

### 跨站请求伪造

- Cross-site request forgery CSRF (sea-surf)
- 利用站点对用户浏览器信任而发起攻击的方式
- 具体实现：
	- 利用 `<script>` 标签来绕开 跨站点之间的资源共享的限制规则
	- 使用 **顶层级** 数组的 JSON，which 是合法的 JavaScript 代码
		
		```json
		[
			{
				"user": "aUser"
			},
			{
				"phone": "123-456-7890"
			{
		]
		```

- [维基百科的一个例子](https://zh.wikipedia.org/wiki/跨站请求伪造)

> 假如一家銀行用以執行轉帳操作的URL地址如下： `http://www.examplebank.com/withdraw?account=AccoutName&amount=1000&for=PayeeName`
那麼，一個惡意攻擊者可以在另一个網站上放置如下代碼： `<img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">`
> 
> 如果有賬戶名為Alice的用戶訪問了惡意站點，而她之前剛訪問過銀行不久，登錄信息尚未過期，那麼她就會損失1000資金。
這種惡意的網址可以有很多種形式，藏身於網頁中的許多地方。此外，攻擊者也不需要控制放置惡意網址的網站。例如他可以將這種地址藏在論壇，博客等任何用戶生成內容的網站中。這意味著如果伺服器端沒有合適的防禦措施的話，用戶即使訪問熟悉的可信網站也有受攻擊的危險。
透過例子能夠看出，攻擊者並不能通過CSRF攻擊來直接獲取用戶的帳戶控制權，也不能直接竊取用戶的任何信息。他們能做到的，是欺騙用戶瀏覽器，讓其以用戶的名義執行操作。
		
### 阻止 CSRF

1. 将敏感信息存储成 JSON 对象 -> 避免使用 **顶层级** 的数组 JSON
	
	```json
	{
		"info": [
			{
				"user": "aUser"
			},
			{
				"phone": "123-456-7890"
			}
		]
	}
	```

2. 仅允许 POST 请求获取数据，禁止使用 GET 请求
	- GET 请求可以直接通过浏览器或 `<script>` 链接到文件


### 跨站脚本攻击

- Cross-site scripting (XSS)
- 注入恶意代码
- 实现原理
	- JavaScript 从服务器取到 JSON 字符串
	- 使用 `eval()` 函数将字符串转换成对象，装入内存
	- JSON 字符串被注入了恶意代码
	- `eval()` 对传入的字符串，无差别地编译执行，执行了恶意代码

### 防止跨站脚本攻击

- 使用 `JSON.parse()` 来转换 JSON 字符串

## 跨域资源共享

- Cross-origin resource sharing (CORS)
- 不受同源策略限制
- 实现原理
	- 服务器在响应头额外加上 `Access-Control-Allow` 属性

	```
	Access-Control-Allow-Credentials:true // 定义证书是否可用
	Access-Control-Allow-Methods:GET, POST // 哪些 HTTP 方法可用
	Access-Control-Allow-Origin:* // 允许哪些域名访问
	```
	
## JSON-P
	
- 带有 padding(内联) 的 JSON
- 利用 `<script>` 不受同源策略影响的特点来向不同站点请求 JSON
- 实现原理 
	- 客户端
		- 在 JavaScript 中声明函数
		- 使用 `<script>` 不受同源策略的影响，创建 `<script>` 标签，动态添加，并将函数名称作为参数传送给服务器
	- 服务端
		- 允许用户自定义函数的名字
		- 根据用户传来的参数(callback)，动态地为在 JSON 内联的函命名 

## JSON 验证工具

- [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/)
- [JSON Editor Online](http://www.jsoneditoronline.org/)
- [JSONLint](http://jsonlint.com/)

