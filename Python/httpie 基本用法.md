# httpie

[项目地址](https://github.com/jakubroztocil/httpie#installation)

## 安装

```shell
pip install httpie
```

## 简单使用

发送请求

```shell
http <url>
```

---

通过指定头部 Accept 字段来控制返回数据格式

返回 json 格式

```shell
http http://127.0.0.1:8000/snippets/ Accept:application/json

# 或者

http http://127.0.0.1:8000/snippets.json
```

返回 html 格式

```shell
http http://127.0.0.1:8000/snippets/ Accept:text/html

# 或者

http http://127.0.0.1:8000/snippets.api
```


通过指定头部 Content-type 字段来控制请求的格式

```shell
# 经试验，--form 的请求数据格式还是会转为 json
http --form POST http://127.0.0.1:8000/snippets/ code="print 123"

http --json POST http://127.0.0.1:8000/snippets/ code="print 456"
```

通过添加 `--debug` 选项来查看请求的构造过程

```shell
http --json --debug POST http://127.0.0.1:8000/snippets/ code="print 456"
```

