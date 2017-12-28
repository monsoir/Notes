# 上传 Python 命令行工具程序到 PyPI

## PyPI

Python Package Index

## 步骤

1. 申请账号
2. 配置账号信息文件
3. 打包项目
4. 上传

### 申请账号

到 [PyPI 主页](https://pypi.python.org/pypi) 中申请账号

### 配置账号信息文件

创建文件 `~/.pypirc` 并写入以下内容

```
[pypi]
username = <刚申请账号的用户名>
password = <账号密码，建议就不要写了，因为是明文存储，就空着好了>
```

### 打包项目

#### 调整项目结构

打包的示例项目为 [safari-bookmarks-jsonizer](https://github.com/Monsoir/safari-bookmarks-jsonizer)

将项目的文件结构调整如下，至少是下面的样子

```
.
├── README.md
├── jsonizer
│   ├── __init__.py
│   ├── cleaner.py
│   ├── extractor.py
│   ├── jsonize.py
│   └── model.py
├── requirements.txt
└── setup.py
```

即

```
.
├── README.md
├── jsonizer  # 包的源代码
│   ├── __init__.py  # 包的模块文件，用来指明这是一个包
│   ├── cleaner.py   # 源代码
│   ├── extractor.py
│   ├── jsonize.py
│   └── model.py
├── requirements.txt
└── setup.py  # 最重要的文件，位于项目根目录，包含各种配置信息
```

#### 配置 `setup.py`

```py
from setuptools import setup, find_packages

setup(
    name='safari-bookmarks-jsonize',
    version='1.0.1',
    keywords=['Safari', 'Bookmarks', 'JSON', 'Monsoir'],
    description='transform Safari bookmarks export to JSON file',
    license='MIT License',

    url='https://github.com/Monsoir/safari-bookmarks-jsonizer',
    author='Monsoir',
    author_email='monwingyeung@gmail.com',

    packages=find_packages(),
    include_package_data=False,
    platforms=["any"],
    install_requires=['beautifulsoup4'],
    python_requires='>3.6',
    entry_points={
        'console_scripts': [
            'jsonize = jsonizer.jsonize:main',
        ]
    }
)
```

> 对于 `setup()` 函数中的各个参数的意义，见最后部分

#### 打包程序

在项目根目录下，运行命令

```sh
python setup.py sdist
```

#### 安装 twine

twine 是一个与 PyPI 进行交互的工具，只支持注册项目和上传项目的功能

> 本来是用 `python setup.py sdist upload` 来上传，但是过程中各种烦人的错误，就直接使用 twine 进行上传了

运行命令

```sh
twine upload dist/* # 将 dist/ 下的所有文件上传到 PyPI
```

> 这个上传命令就会用到了之前用户名和密码的配置文件

## 安装

最后，我们可以通过 `pip` 来安装命令行程序了

```sh
pip install safari-bookmarks-jsonizer
```

## `setup()` 函数的部分参数意义

- `name` 项目名称
    - 只能包含 ASCII 字符，数字，`_`, `-`, `/`, `.`
- `url` 项目代码地址
    - 必须正确填写，如果填写错误，会导致上传失败
- `classifiers` 项目分类器
    - 通常是用来指明开发使用的 Python 版本
    - 这个参数只会用来对项目进行分类和索引，对项目本身的安装，运行不会有影响
    - 完整的分类标识见 [classifiers](https://pypi.python.org/pypi?%3Aaction=list_classifiers)
- `packages`
    - 列出项目运行时需要包含的包，不包括第三方的
    - 可以用列表逐个枚举出来
    - 也可以使用 `setup` 模块中的 `find_packages()` 函数自动填充
- `install_requires`
    - 项目通过 `pip` 安装时，能正常运行的最小依赖集合
    - 需要与 Requirements files 区别，which 全部依赖的集合
    - 区别见 [install_requires vs Requirements files](https://packaging.python.org/discussions/install-requires-vs-requirements/#install-requires-vs-requirements-files)

> 与 node.js 联动
> `install_requires` 类似 packages.json 中的 `dependencies`
> `requirements.txt` 类似 packages.json 中的 `dependencies` + `dev_dependencies`

- `python_requires` 程序正常运行的 Python 版本
- `package_data` 程序包内的资源文件
- `data_files` 程序包外的资源文件
- `entry_points` 定义程序的各种入口
    - 通常配合 `console_scripts` 使用

```py
entry_points={
    'console_scripts': [
        'sample=sample:main',
        # 可以定义多个入口
        # command=module1.module2:entry_function
    ],
},
```

## References

- [Packaging and Distributing Projects](https://packaging.python.org/tutorials/distributing-packages/#configuring-your-project)
- [twine](https://pypi.python.org/pypi/twine)


