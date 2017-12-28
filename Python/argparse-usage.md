# argparse 使用

Python 制作命令行工具时，读取命令的参数有3种：

- 使用 `sys.args`
- 使用 `getopt`
- 使用 `argparse`

最后发觉，`argparse` 是最好用的一个，只需要配好参数，然后各种提示就会自动生成。缺点就是需要了解的参数也比较多

这里主要记录一下添加参数的方法 `add_argument()` 中的参数意义

其中实践项目是 [picturizer](https://github.com/Monsoir/picturizer)

## argparse 使用方法

```py
import argparse

# 创建一个负责参数转换的对象
parser = argparse.ArgumentParse(prog='picturize')

# 添加需要的参数选项
parser.add_argument(
    '-i', # 短格式选项
    '--input', # 长格式选项
    metavar='source_path', # 参数占位说明符
    type=str, # 参数数据类型
    action='store', # 参数存储方式，值为常量，见文档
    dest='input_file', # 参数存储变量名
    required=True, # 是否强制需要
    nargs='?', # 参数消费个数，? 代表一个
    help='input file', # 选项帮助说明

```

## `add_argument()`

函数原型，一大串

```py
ArgumentParser.add_argument(name or flags...[, action][, nargs][, const][, default][, type][, choices][, required][, help][, metavar][, dest])
```

### 比较重要的参数们

- 如上面的例子，直接填写选项的名称，短格式，长格式一起来
- `metavar` 参数占位符说明，这个值到时会在命令格式说明中出现
- `type` 参数数据类型，默认是 `str`
- `action` 获得参数之后的行为，这个值是标准库中定义好的，虽然是手写字符串，但也不能随便写，同时，可以自定义一些 `aciton`, 预先定义的有
    - `store` 默认行为，把参数值存储起来
    - `store_const` 存储一个常量，需要与 `const` 参数一起使用
        - 当 `action` 指定为 `store_const` 时，就会把参数 `const` 值存储到对应的命令选项参数中
    - `store_true`
    - `store_false` 顾名思义，就是将值设置为 `True` 或 `False`
    - `append` 对应的选项参数存储的是一个 `list` 类型数据
        - 当 `action` 指定为 `append` 时，会将参数值添加到一个 `list` 中
    - `append_const` 与 `append` 类似，但是添加到 `list` 的值是有 `const` 参数决定
    - `count` 对选项在命令中出现的次数进行统计
- `nargs` 指定选项将会吸收多少个后面接上的参数，选项值也是协商好的，不能乱填
    - `N` 将选项后面接的字符串，组成一个 `list` 类型进行赋值
    - `?` 吸收一个参数值
    - `*` 将吸收所有的值，并将参数按照位置分配下去，参数过多的时候，会将参数分配到没有显示指明的参数中
    - `+` 与 `*` 类似，但当参数过多的时候，会产生错误
    - `argparse.REMAINDER` 吸收完能对上号的参数后，将剩下的参数全部分配到一个 `list` 中
- `const` 与 `action` 中的 `store_const`, `append_const` 配合使用
- `default` 默认值
- `required` 是否必填
- `help` 选项的帮助说明
- `dest` 用来重命名参数变量

### 示例

```sh
$ picturize -h

usage: picturize [-h] -i [source_path] [-o [store_path]] [-s [a_scale]]

optional arguments:
  -h, --help            show this help message and exit
  -i [source_path], --input [source_path]
                        input file
  -o [store_path], --output [store_path]
                        output file
  -s [a_scale], --scale [a_scale]
                        scale of the picture, should be 0 < scale <= 1
```

## References

- [argparse — Parser for command-line options, arguments and sub-commands](https://docs.python.org/3/library/argparse.html)
- [The add_argument() method](https://docs.python.org/3/library/argparse.html#the-add-argument-method)

