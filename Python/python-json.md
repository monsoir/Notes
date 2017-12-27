# Python 处理 JSON

Python 中使用 json 模块对 JSON 数据进行处理

## 读取 JSON 数据的字符串

使用 `json.loads()` 函数

```py
import json

JSONDataAsPythonDictionary = json.loads(aJSONDataString)
```

> `json.loads()` 中的 `loads` 意思是 load string

## 生成 JSON 数据

使用 `json.dumps()` 函数

```py
import json

JSONDataAsString = json.dumps(PythonValue)
```

其中，PythonValue 的类型只能为 Python 的基本数据类型之一

- 字典
- 列表
- 整型
- 浮点型
- 字符串
- 布尔型
- None

> `json.dumps()` 中的 `dumps` 意思是 dump string

