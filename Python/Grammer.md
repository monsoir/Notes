# Python Grammer

- [格式化输出](#格式化输出)
- [switch 的实现](#switch-的实现)

## 格式化输出

- `format()`

	```py
	# 字符串后使用 `.` 来设置参数
	print('{0} is {1} years old. '.format(name, age))
	```

## switch 的实现

Python 中没有像其他语言一样的 switch 条件语句，但是可以使用 Python 的字典来实现相同的效果

### switch 只返回单个值，如字符串

```py
def switchValue(n):
    switcher = {
        1: 'one',
        2: 'two',
        3: 'three',
    }

    return switcher.get(n, 'none')

if __name__ == '__main__':
    print(switchValue(3))
    print(switchValue(5))
```

通过字典的 `get` 方法，同时可以指定默认的返回值，当字典中找不到对应的值时，返回默认值

### switch 函数执行

```py
# 下面3个函数是在 switch 中调用的
def sayHello(who):
    print("hello, %s" % who)

def sayGoodbye(who):
    print('goodbye, %s' % who)

def saySomething(who):
    print('something, %s' % who)
    
def switchFunction(funcName, args):
    switcher = {
        'hello': sayHello,
        'goodbye': sayGoodbye,
        'something': saySomething,
    }

    func = switcher.get(funcName, 'pass')
    if (callable(func)): func(args)
    else: pass

if __name__ == '__main__':
    switchFunction('hello', 'Alice')
    switchFunction('goodbye', 'Christina')
    switchFunction('something', 'Cortana')
    switchFunction('destroy', 'Cathrine')
```

通过识别字符串，返回函数名，注意返回的是函数名，在 Python 中函数是可以作为一个变量来使用

同时，还可以直接传递参数



