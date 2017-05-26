# Django REST - Requests and Response

## Request

REST 提供 `Request` 对象，是对普通的 `HttpRequest` 对象的一个拓展，提供更灵活的请求转换

`Request` 的核心功能是 `request.data` 属性，它类似于 `request.POST`

`request.POST` 只处理 form 数据，并只与 POST 方法配合
`request.data` 可以处理多种数据，并可与 POST, PUT, PATCH 等方法配合

## Response

REST 提供 `Response` 对象，是 `TemplateResponse` 的一种类型，但处理未经渲染的数据，并根据数据内容来决定返回到请求方的数据格式

```py
return Response(data)
```

## 状态码

status 模块中定义了状态码，如 `HTTP_400_BAD_REQUEST`


## 封装 API 视图

REST 提供两种封装形式来编写 API 视图

- 使用 `@api_view` 装饰器封装基于方法的 API 视图
- 继承 `APIView` 来封装基于类的 API 视图

这两种封装形式都提供了一些功能，如

- 确保编写的 API 视图接收到 `Request` 实例
- 向 `Response` 实例中注入 context(上下文)，使得可以根据数据内容确定响应数据的格式
- 返回 `405 Method Not Allowed`
- 当 `request.data` 输入有误时，处理 `ParseError` 异常

### 使用 @api_view 构建 API 视图

引入模块

```py
from rest_framework.decorators import api_view
```

---

使用装饰器

```py
@api_view(['GET', 'POST'])
def snippet_list(request):
	...
```

其中 `request.data` 可以处理 json 和其他格式的数据，不需要我们关心

#### 使用动态的 URL 后缀

使用动态的 URL 后缀，我们就不用担心 URL 的后缀，如 `.json`，`.api`

稍微修改 API 函数就可以实现

```py
@api_view(['GET', 'POST'])
def snippet_list(request, format=None):
	...
```

---

同时，修改 app 的 urls.py

引入模块

```py
from rest_framework.urlpatterns import format_suffix_patterns
```

处理 urlpatterns

```py
urlpatterns = [
	...
]

urlpatterns = format_suffix_patterns(urlpatterns)
```

这样，请求的 url 可以以 `.json` 或 `.api` 结尾

> format_suffix_patterns 需要 API 视图函数在参数列表中添加 `format` 关键字参数

关于 format_suffix_patterns 参考 [官方文档](http://www.django-rest-framework.org/api-guide/format-suffixes/)

