# Python 图像处理

- 在 Python 中，处理图像可以使用第三方模块 Pillow

## Pillow

- 在 Pillow 中，一个 RGBA 值表示为 4 个整数值的元组，如 (255, 0, 0, 255)
- 在 Pillow 中，通常需要一个矩形的数据，这个矩形的数据由一个 4 个整数组成的元组表示：
    - 左，矩形最左边的 x 坐标
    - 顶，矩形的顶边 y 坐标
    - 右，矩形最右边的 x 坐标，实际矩形的范围不包括这个值
    - 底，矩形的底边 y 坐标，实际矩形的范围不包括这个值
    - 原点位于矩形左上角，向下为 y 的正方向，向右为 x 的正方向

- 安装 Pillow

    ```shell
    pip install pillow
    ```

- Pillow 的模块名为 PIL

### 获取颜色的 RGBA 值

```py
from PIL import ImageColor
color = ImageColor.getcolor('red', 'RGBA')
```

- 返回的 color 是使用 4 个整数值的元组
- 传入的颜色名称不限定大小写

### 加载图像

```py
from PIL import Image

anImage = Image.open(imagePath)
```

- 由于设计问题，引入 Image 须使用 `from PIL import Image`
- 返回的 anImage 是一个 Image 对象，在这个对象上，可以
    - save(), 保存对图片的任何修改，对任何文件都适用
    - 旋转，调整大小，裁剪，绘画等操作

### 获取图像数据

```py
anImage.size # 获取大小，返回的是 (width, height) 元组

anImage.filename # 获取文件名，包含后缀名

anImage.format # 获取图片格式

anImage.format_description # 获取图片格式的定义

anImage.save('xxx.jpg') # 可以直接指定后缀名来对图像进行格式转换
```

### 新建图像

```py
from PIL import Image

im = Image.new('RGBA', (100, 200), 'purple')
im.save('image.png')
```

参数

- 颜色模式
- 大小
- 起始背景颜色，可以是
    - RGBA 值元组，或使用 `ImageColor.getColor()` 来获取颜色值元组
    - 颜色名称字符串

> 若不指定颜色，则默认是 (0, 0, 0, 0), 即透明色


