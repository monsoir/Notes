# POST 请求

POST 是向 HTTP 服务器提交数据的方法

HTTP 请求包含三个部分：

- 请求方法：GET, POST
- 请求头
- 请求正文

```
<method><request-URL> <version>
<headers>

<entity-body>
```

# POST 请求中的 Content-Type

## application/x-www-form-urlencoded

- 默认方法
- 请求体会将请求参数进行 URL 编码，即像 GET 方法一样，对参数进行编码，不同之处在于编码后的参数并不是像 GET 一样拼接在 URL 后面，而是放在请求体中
	- 编码格式 key1=value1$key2=value2

## multipart/form-data

- 可用于上传较复杂的数据，二进制数据
- 必须在请求头中的 Content-Type 中显式指明
- 请求正文中，必须规定一个内容分隔符，用于分割内容
- 因此，Content-Type="multipart/form-data;boundary=${bound}"，${boundary} 就是分隔符的内容，并且上传的内容中不能包含与 ${boundary} 相同的值

## application/json

- 用于上传 JSON 格式的数据，而且数据结构比较复杂的，如数组里面包含了各种字典的
- 需要服务端的支持


