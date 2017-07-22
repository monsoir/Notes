# Python 组织文件

- [使用 shutil 模块](#shutil-模块)
    - [复制文件和删除文件夹](#复制文件和删除文件夹)
    - [移动和改名文件和文件夹](#移动和改名文件和文件夹)
    - [永久删除文件和文件夹](#永久删除文件和文件夹)
    - [send2trash](#send2trash)
- [遍历目录树](#遍历目录树)
- [解压/压缩文件](#解压压缩文件)
    - [读取 ZIP 文件](#读取-zip-文件)
    - [解压文件](#解压文件)
    - [创建和添加到 ZIP 文件](#创建和添加到-zip-文件)
- [结构图](#结构图)

## shutil 模块

### 复制文件和删除文件夹

- shutil.copy(source, dest)
    - 参数都为路径
    - 返回被复制文件的目标路径
- shutil.copytree(source, dest)
    - 复制整个文件夹
    - 参数都为路径
    - 返回新复制的文件夹的路径

### 移动和改名文件和文件夹

- shutil.move(source, dest)
    - 返回新位置的绝对路径
    - 若 dest 为文件夹，则会将 source 移动到 dest 中，保持原有的文件名
    - dest 中的文件名必须存在，否则抛异常

### 永久删除文件和文件夹

shutil 和 os 中都有删除文件和文件夹的操作，但具体效果有所不同

- os.unlink(path)
    - 删除 path 处的文件
- os.rmdir(path)
    - 删除 path 处的文件夹
    - path 处的文件夹必须为空，其中没有任何文件和文件夹
- shutil.rmtree(path)
    - 删除 path 处的文件夹
    - 删除内容包含了 path 下的所有文件和文件夹

> 编写删除操作的代码时，先构建一个版本，用 `print` 来代替删除操作，打印出要删除的文件名，确认无误后，使用真正的删除代码来替换 `print`

### send2trash

执行 shutil 中的删除操作是危险的，因为 shutil 的删除操作是不可逆的

第三方模块 send2trash 可以将删除的文件发送到系统的垃圾箱，并不是立即的永久删除

```shell
# 安装
pip install send2trash
```

```py
# 执行删除操作
import send2trash

send2trash.send2trash(path)
```

> send2trash 只能将文件发送到垃圾箱，但 send2trash 自身不能将文件从垃圾箱发送回原位置

## 遍历目录树

使用 `os.walk(path)` 函数

### os.walk(path)

- path 为一个文件夹的路径
- 在每次的循环迭代中，返回 3 个值
    - 当前文件夹的名称
    - 当前文件夹中子文件夹的列表
    - 当前文件夹中文件的列表

```py
import os

for folderName, subFoleders, fileNames in os.walk('aPath'):
    # do something with current folder's name
    
    for subFolder in subFolders:
        # do something with sub folder's name in current folder
    
    for fileName in fileNames:
        # do something with files's names in current folder
```

## 解压/压缩文件

使用到 `zipfile` 模块

### 读取 ZIP 文件

1. 创建 `ZipFile` 对象

    ```py
    import zipfile
    
    aZipFile = zipfile.ZipFile('aPath')
    ```

    
2. 调用 `ZipFile` 的方法来查看 ZIP 文件的信息
    - namelist() 返回 ZIP 文件中包含的所有文件和文件夹的字符串列表
    - getInfo(fileName)
        - fileName 为 `namelist()` 回结果中的一个文件名
        - 返回一个 `ZipInfo` 对象
            - `aZipInfo.file_size()`
            - `aZipInfo.compress_size()`

3. 关闭 ZipFile 对象
    - `aZipFile.close()`

### 解压文件

#### 从 ZIP 文件中解压所有文件和文件夹

- aZipFile.extractall(path)
    - 默认将解压内容存放到当前工作目录
    - 可以传入一个路径参数，指定解压内容存放位置

#### 解压 ZIP 文件中的单个文件

- aZipFile.extract(fileName, path)
    - 第一个参数为 namelist() 中返回结果中的一个文件名
    - 可以传递第二个参数作为解压内容存放的位置

### 创建和添加到 ZIP 文件

1. 使用写模式创建 `ZipFile` 对象

    ```py
    import zipfile
    
    newZip = zipfile.ZipFile('zip name', 'w')
    ```

    传入压缩文件的名称，并使用 `w` 模式

    也可以选用 `a` 添加模式来向原有的压缩文件添加压缩内容

2. 调用 `write()`
    
    ```py
    newZip = write('file name', compress_type=zipfile.ZIP_DEFLATED)
    ```

    传入要压缩的文件，并指定采用的压缩算法

3. 关闭 `ZipFile` 对象

    ```py
    newZip.close()
    ```

## 结构图

![](./assets/python-file-organize.png)

