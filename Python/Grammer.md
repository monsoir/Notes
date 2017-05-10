# Python Grammer

- [格式化输出](#格式化输出)
- [switch 的实现](#switch-的实现)
- [sum](#sum)
- [iter](#iter)
- [zip](#zip)
- [map](#map)

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


## sum

函数原型

```
sum(iterable[, start])
```

[官方说明文档链接](https://docs.python.org/3/library/functions.html?highlight=sum#sum)

配合 [这个源码](https://github.com/Monsoir/LeetCode/blob/master/Solutions/566%20Reshape%20the%20Matrix.md) 看

start 默认值为 0

因此，下面的代码

```py
sum([1,2])
```

等价于 这里有个 0 -> 0 + 1 + 2

---

显式指定 start

```py
sum([1,2], 3)
```

等价于 1 + 2 + 3

---

然而，start 只是默认为 0，也可以指定其他类型的数据（但不能是字符串，会抛异常），例如上面的用到的 

```py
flat = sum(nums, [])
```

这里通过指定 start 为 []，其实指定其他的 list 也是可以的，如 [4, 5]，可以轻松地将二维数组转化为一维数组，避免了循环遍历的臃肿写法

上面的写法，等价于 [] + nums
应该注意的是，start 值是从最前面开始加的


## iter

函数原型

```
iter(object[, sentinel])
```

[官方说明文档](https://docs.python.org/3/library/functions.html#iter)

配合 [这个源码](https://github.com/Monsoir/LeetCode/blob/master/Solutions/566%20Reshape%20the%20Matrix.md) 看

返回一个迭代器

## zip

函数原型

```
zip(*iterables)
```

[官方说明文档](https://docs.python.org/3/library/functions.html#zip)

配合 [这个源码](https://github.com/Monsoir/LeetCode/blob/master/Solutions/566%20Reshape%20the%20Matrix.md) 看


接受一个可迭代的对象，并进行拉链式的合并，如：

```py
a = [1, 2, 3]
b = [4, 5, 6]
zip(a, b) # [(1, 4), (2, 5), (3, 6)]
```

这个 `zip` 的原理，就是将若干个可迭代对象中，相同索引的元素合并成一个新的元素，最后将这些新元素合并成一个 tuple 返回

若这干个可迭代的个数不一样，则以最短的为准（即会把非最短的对象进行截取）

---

应注意到，`zip` 的定义中，参数前面带了 `*` 运算符

[官方文档对 * 运算符的说明](https://docs.python.org/3/tutorial/controlflow.html#unpacking-argument-lists)

`*` 运算符用于解构对象(unpacking)，即将封装好在数据结构中的对象拆开，如，用于 range 函数中

```py
range(3, 6)

# 如果范围被装好在一个 list 中，那可以通过解构，达到相同的效果
args = (3, 6)
range(*args)
```

所以，当使用 `zip` 时，一般是将一个封装好的可迭代对象作为参数

---

现在，到了最难的一部分

```py
tuples = zip(*([iter(flat)] * c))
```

这里的传入的参数，经过了一下处理：

1. flat，通过 `iter()` 方法，将 list 类型转为了一个迭代器
2. 将这个 迭代器 复制了 c 次，并将 c 个迭代器包装在 list 中
3. 将包含 c 个迭代器的 list 传到 `*` 进行解构

有一个困扰颇久的点，经过 Stack Overflow 上启发，有所领悟

应该注意的是，最后的 list，是装载着 c 个迭代器，但是，这 c 个迭代器，具有相同的指针，指向了相同的对象

因此，更准确的说，最后的 list，是装载着 c 个迭代器的指针

由于这 c 个迭代器指针指向了相同的迭代器，因此，这 c 个迭代器(实际上 1 个，必须牢记这一点)共享了迭代器的状态

因此，当 `zip` 对这 c 个迭代器进行拉链的时候，实际上是只是对 1 个迭代器进行拉链

对迭代器中元素进行迭代时，**每个元素的状态只有一个，每个元素只会消耗一次**

```
1,2,3,4,5,6,7,8,9  1,2,3,4,5,6,7,8,9  1,2,3,4,5,6,7,8,9
^                    ^                    ^            
      ^                    ^                    ^
            ^                    ^                    ^
```

于是，就会出现了上图的效果，得出的结果是 

```
((1, 2, 3), (4, 5, 6), (7, 8, 9))
```

而不是

```
((1, 1, 1), (2, 2, 2), (3, 3, 3))
```

> 总结：
> 
> 当参数中，可迭代对象是不同的对象时，如前面谈论的情况，那么 zip 的原理可以简单地看作为：
> 
> ✔︎ 将不同的可迭代对象中，相同索引的值合并成一个新元素，最后返回这些新元素组成的 tuple
> 
> 但当参数重，可迭代对象是相同对象时，那么 zip 的原理就需要考虑到
> 
> ✔︎ 这些可迭代对象其实是同一个迭代器，同一个迭代器中，状态共享的问题

##### Reference

[参考1: http://blog.csdn.net/zhu_liangwei/article/details/7967237](http://blog.csdn.net/zhu_liangwei/article/details/7967237)
[参考2 Stack Overflow 一言惊醒梦中人: http://stackoverflow.com/a/30380585/5211544](http://stackoverflow.com/a/30380585/5211544)
[参考3 Stack Overflow 迭代过程: http://stackoverflow.com/a/2233827/5211544](http://stackoverflow.com/a/2233827/5211544)

## map

函数原型

```
map(function, iterable, ...)
```

[官方说明文档](https://docs.python.org/3/library/functions.html#map)

配合 [这个源码](https://github.com/Monsoir/LeetCode/blob/master/Solutions/566%20Reshape%20the%20Matrix.md) 看

为第二个参数中的可迭代对象中的元素，调用第一个参数中传入的函数，省略号是函数的参数，如果有提供，则调用函数的时候会用到

```py
map(list, tuples)
```

为 tuples 中的每个元素，调用 list 方法，将每个元素转为 list 类型


