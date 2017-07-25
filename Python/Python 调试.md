# Python 调试

- [异常的简单使用](#异常的简单使用)
- [取得反向跟踪的字符串](#取得反向跟踪的字符串)
- [断言](#断言)
- [使用日志](#使用日志)

## 异常的简单使用

### 抛出异常

- `raise` 关键字
- 调用 `Exception()` 函数
- 传递字符串给 `Exception()` 函数，该字符串意味着有用的出错信息

```py
raise Exception('An exception')
```

### 捕获异常

- 使用 try/except 语句

    ```py
    try:
        # do something
    except Exception as err:
        # do something with the exception
    ```
    
    若产生一个 Exception 对象，则将异常对象存储到 err 中

## 取得反向跟踪的字符串

使用 traceback 模块，可以将未得到及时合理处理的异常进行跟踪，必要时将异常信息记录到文件中

```py
import traceback

try:
    # do something
except:
    # ...
    anErrorFileObject.write(traceback.format_exc())
    # ...
```

## 断言

确保代码没有做明显错误的事情，若检查失败，则抛出异常，使用断言

1. `assert` 关键字
2. 条件（求值为 True 或 False 的表达式）
3. 逗号
4. 当条件为 False 时显示的字符串

```py
assert(1==2, r'this string will be printed when condition is Flase')
```

> 断言是面向开发的，在运行脚本时，可以传入 -O 选项禁用断言

## 使用日志

启用日志模块 logging, 使用步骤

1. 引入 logging 模块
2. 定义日志输出格式
3. 在需要的地方调用日志输出

```py
# 引入模块
import logging

# 定义日志输出格式
loggin.basicConfig(level=loggin.DEBUG, format=' %(asctime)s - %(levelname)s - %(message)s')

# 调用日志输出
loggin.debug(r'say something')
```

`loggin.debug` 会调用 `logging.basicConfig` 方法中定义的格式来输入日志

> 最好不用 print() 进行调试或打印不必要的信息
> 
> logging 方式的日志输出时可以一键关闭的，而 print() 方式是需要手动逐行删除
> 
> 即，loggin 模块使得日志信息的输出开关切换更加方便准确

### 日志级别

级别 | 相应调用的函数 | 描述
--- | --- | ---
DEBUG | logging.debug() | 最低级别，用于诊断问题
INFO | logging.info() | 用于记录一般事件 或 确认工作正常
WARNING | logging.warning() | 用于表示可能的问题，该问题不会阻止程序工作，但以后不好说
ERROR | logging.error() | 记录错误，导致程序做某件事失败了
CRITICAL | logging.critical() | 最高级别，表示致命错误

可以通过在 logging.basicConfig 中指定 level 参数，即日志级别，来控制显示日志的类型

> 控制日志显示类型
> 
> 指定的 level, 只会忽略级别低的日志，级别比指定类型高的日志仍然会输出



### 禁用日志

调用 `logging.disable(日志级别)` 来禁止该级别和更低级别的日志输出

> 禁用日志时，更好的做法是将 `logging.disable` 放置在接近 `import logging` 的地方，因为 `logging.disable` 只会对其后的代码生效，这也就是脚本的一个特性了

### 将日志记录到文件

在 `logging.basicConfig()` 中指定 `filename` 参数

```py
import logging
logging.basicConfig(filename='LogFile.txt', level=logging.DEBUG, format='...')

# 日志将会输出到 LogFile.txt 文件中
```


