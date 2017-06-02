
# Django REST - Class-based Views 

## 最普通的那种

需要引入

```py
from rest_framework.views import APIView
```'

---

每一个 URL 对应一个 class，该 class 继承 APIView，如

```py
class SnippetList(APIView):
	...
```

---

HTTP 中的请求方法对应 class 中的相应方法

```py
def get(self, request, format=None):
	...

def post(self, request, format=None):
	...
```

---

使用 class-based view，还需要对 App 内的 urls.py 进行改造

```py
urlpatterns = [
    url(r'^snippets/$', views.SnippetList.as_view()),
    url(r'^snippets/(?P<pk>[0-9]+)/$', views.SnippetDetail.as_view()),
]
```

使用 `as_view()` 方法

## 次高级形态：使用 Mixin

不同的需求，create / retrieve / update / delete 方法一般具有共性，因此可以共享一些相同的行为

利用 Python 的多重继承特点，可以实现这些相似行为的共享

```py
from rest_framework import mixins
from rest_framework import generics
```

```py
class SnippetDetail(mixins.RetrieveModelMixin,
                    mixins.UpdateModelMixin,
                    mixins.DestroyModelMixin,
                    generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```

其中，GenericAPIView 提供了核心功能，而不同的 Mixins 提供不同的方法

- ListModelMixin -> `.list()`
- CreateModelMixin -> `.create()`
- RetrieveModelMixin -> `.retrieve()`
- UpdateModelMixin -> `.update()`
- DestroyModelMixin -> `.destroy()`

## 最高级形态：使用 generic class-based view

Django Rest Framework 中提供一种已经 mixin 的 view 类，可以直接用
 
```py
from rest_framework import generics
```

```py
class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
```

关于 Generic class-based view 参考 [官方文档](http://www.django-rest-framework.org/api-guide/generic-views/)


