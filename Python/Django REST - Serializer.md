# Django REST - Serializer

## 创建一个 Model

```py
from django.db import models
from pygments.lexers import get_all_lexers
from pygments.styles import get_all_styles

LEXERS = [item for item in get_all_lexers() if item[1]]
LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
STYLE_CHOICES = sorted((item, item) for item in get_all_styles())


class Snippet(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100, blank=True, default='')
    code = models.TextField()
    linenos = models.BooleanField(default=False)
    language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
    style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

    class Meta:
        ordering = ('created',)
```

Django 中 Model 都继承于 django.db 的 models

关于 model 的字段类型，以及参数的查阅，可参考 [官方文档](https://docs.djangoproject.com/en/1.11/ref/models/fields/)

创建完 model 之后，需要对 model 进行 migrate

```shell
python manage.py makemigrations <app 名称>
python manage.py migrate
```

## Serializer

Serializer 用于将数据对象进行序列化和反序列化到特定的数据格式，如 json

```py
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES


class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

    def create(self, validated_data):
        """
        Create and return a new `Snippet` instance, given the validated data.
        """
        return Snippet.objects.create(**validated_data)

    def update(self, instance, validated_data):
        """
        Update and return an existing `Snippet` instance, given the validated data.
        """
        instance.title = validated_data.get('title', instance.title)
        instance.code = validated_data.get('code', instance.code)
        instance.linenos = validated_data.get('linenos', instance.linenos)
        instance.language = validated_data.get('language', instance.language)
        instance.style = validated_data.get('style', instance.style)
        instance.save()
        return instance
```

Serialzier 都需要继承于 rest_framework 的 `serializers.Serializer`

继承于 `serializers.Serializer` 的类，需实现 `create()` 和 `update()` 方法，当调用 `serializer.save()` 是，将会用到这两个方法

### 如何使用 Serializer

引入 model, serializer, JSONRender, JSONParser 模块

```py
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework.renderers import JSONRenderer # 将 Python 原生类型数据转为 JSON
from rest_framework.parsers import JSONParser # 将 JSON 转为 Python 原生类型数据
```

---

创建 model 实例，并保存

```py
snippet = Snippet(code='foo = "bar"\n')
snippet.save()
```

---

将 model 数据转为 Python 原生数据

```py
serializer = SnippetSerializer(snippet)
serializer.data # 已经将 model 数据转为了 Python 的原生数据，如字典
```

---

序列化数据为 json 数据

```py
content = JSONRenderer().render(serializer.data)
content # 已转为 JSON 形式的二进制数据
```

---

反序列化数据为 Python 原生对象

```py
from django.utils.six import BytesIO

stream = BytesIO(content)
data = JSONParser().parse(stream)
data # 已转为 Python 的原生类型
```

将 Python 原生类型数据转为 model 数据

```py
serializer = SnippetSerializer(data=data)
serializer.is_valid()

serializer.validate_data # 返回已验证的数据

serializer.save() # 生成 Snippet 对象
```

若需要从一堆数据中生成一堆对象，设置 `many=True`

```py
serializer = SnippetSerializer(Snippet.objects.all(), many=True)
serializer.data
```

## ModelSerializer

创建普通 Serializer 的快捷方法

```py
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = ('id', 'title', 'code', 'linenos', 'language', 'style')
```

## 编写视图

其中包含了 Restful 的 GET, POST, DELETE 的方法

## 连接视图与 URL

在 App 中创建一个 urls.py 文件，用于编写 App 内的视图与 URL 的连接

```py
from django.conf.urls import url
from snippets import views

urlpatterns = [
	url(r'^snippets/$', views.snippet_list),
	url(r'^snippets/(?P<pk>[0-9]+)/$', views.snippet_detail),
]
```

---


在项目的 urls.py 文件中将 App 的 urls.py 接入

```py
from django.conf.urls import url, include

urlpatterns = [
	url(r'^', include('snippets.urls')),
]
```

## 关系图

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fg8fgyw61wj31kw0azaee.jpg)

