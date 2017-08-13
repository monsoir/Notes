# Visual Studio Code 配置 Django 开发环境

## 安装 Python 端库

```sh
# Python 静态语法检测器
pip install pylint

# 使用于 Django 项目的语法检查插件
pip install pylint-django

# 代码格式化工具
pip install autopep8
```

> 可以将这些库写到 requirements.txt 文件，随后使用 `pip install -r requirements.txt` 进行安装

## 修改 Visual Studio Code 端的配置文件

```json
{
    "python.pythonPath": "<虚拟环境下的 Python 执行路径>",
    "python.linting.pylintPath": "pylint",
    "python.linting.pylintArgs": [
        "--load-plugins", "pylint_django"
    ],
    "python.formatting.autopep8Path": "autopep8"
}
```

