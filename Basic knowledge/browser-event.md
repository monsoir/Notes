# 浏览器事件

- UI 事件，元素交互时
- 焦点事件，元素获得或失去焦点时
- 滚轮事件，使用滚轮时
- 文本事件，输入文本时
- 键盘事件，通过键盘在页面执行操作时
- 合成事件，IME(Input method Editor) 输入法编辑器输入字符时
- 变动事件，底层 DOM 结构发生变化时
- ~~变动名称事件，废弃，忽略~~

## UI 事件

- load
    - 页面完全加载后在 window 上触发
    - 图像加载完后在 `<img>` 上触发

- unload
    - 页面完全卸载后在 window 上触发
    - 用户从一个页面切换到另一个页面，清楚引用，防止内存泄漏

- abort
    - 用户停止下载过程时，嵌入内容没有加载完，在 `<object>` 元素上触发

- error
    - 发生 JavaScript 错误时，在 window 上触发
    - 无法加载图像时，在 `<img>` 上触发

- select
    - 用户选择文本框 `<input>` 或 `<textarea>` 中的一个或多个字符时触发

- resize
    - 窗口或框架大小变化时，在 window 或框架上触发
    - 浏览器窗口最大化与最小化时也会触发事件

- scroll
    - 用户滚动带滚动条的元素中的内容时，在对应的元素上触发
    - 最外层的元素滚动条位于 `<body>` 中
    - 相关属性 `scrollLeft`, `scrollTop`

## 焦点事件

判断元素获得焦点的状态的相关方法

- `document.hasFocus()`
- `document.activeElement`

---

事件

- blur
    - 元素失去焦点时触发，不会冒泡，所有浏览器都支持
    - 但可以在事件捕获中侦探到

- focus
    - 元素获得焦点时触发，不会冒泡，所有浏览器都支持
    - 但可以在事件捕获中侦探到

- focusin
    - 与 focus 等价，但冒泡

- focusout
    - blur 的通用版本

---

焦点从一个元素移动到另一个元素，依次触发事件

1. `focusout` 在失焦元素上触发
2. `focusin` 在获焦元素上触发
3. `blur` 在失焦元素上触发
4. `focus` 在获焦元素上触发

---

确定浏览器是否支持上述事件，检测代码

```js
var isSupported = document.implementation.hasFeature("FocusEvent", "3.0");
```

## 鼠标与滚轮事件

- click
    - 鼠标单击或按下回车键触发

- dblclick
    - 双击鼠标触发

- mousedown
    - 按下鼠标任意按钮时触发，不能通过键盘触发

- mouseenter
    - 光标从元素外部首次移动到元素范围内时触发，不冒泡
    - 光标移动到后代元素也不会触发

- mouseleave
    - 元素上的光标移动到元素范围外时触发，不冒泡
    - 光标移动到后代元素也不会触发

- mousemove
    - 鼠标指针在元素内移动时，会重复地触发
    - 不能通过键盘触发

- mouseout
    - 鼠标指针位于一个元素上，用户将指针移入到另一个元素时触发
    - 不能通过键盘触发
    - 这个事件会涉及到两个元素，另外一个元素，可以通过 `event.relatedTarget` 获取

- mouseover
    - 鼠标指针开始位于一个元素外部，然后用户将鼠标首次移入另一个元素边界内时触发
    - 不能通过键盘触发
    - 这个事件会涉及到两个元素，另外一个元素，可以通过 `event.relatedTarget` 获取

- mouseup
    - 释放鼠标时触发
    - 不能通过键盘触发

- mousewheel
    - 鼠标滚轮事件

> 所有元素都支持鼠标事件，除了 mouseenter 和 mouseleave, 其他鼠标事件都会冒泡，也可以被取消
> 
> 在同一个元素上，只有相继触发 mousedown 和 mouseup 事件，才会触发 click 事件
> 只有触发两次 click 事件，才会触发一次 dblclick 事件

---

鼠标按键按下的事件发生顺序

1. mousedown
2. mouseup
3. click
4. mousedown
5. mouseup
6. click
7. dblclick

---

### 坐标类型

- 客户区坐标位置
    - 范围：浏览器视口
    - 属性：`event.clientX`, `event.clientY`

- 页面坐标位置
    - 范围：页面，包括非可视范围的那部分
    - 属性：`event.pageX`, `event.pageY`

- 屏幕坐标位置
    - 范围：显示屏屏幕
    - 属性：`event.screenX`, `event.screenY`

### 修改键

- Shift
- Ctrl
- Alt
- Meta(Windows 下为 Windows 键，Mac 下为 cmd 键)

这四个键分别为四个布尔值，检测哪个键被按下，就判断哪个布尔值为 `true`

- event.shiftKey
- event.ctrlKey
- event.altKey
- event.metaKey

## 键盘与文本事件

### 键盘事件

- keydown
    - 用户按下 **任意键**，按着不放，会重复触发
    - 文本框发生变化前触发

- keypress
    - 用户按下键盘 **字符键**，按着不放，会重复触发
    - 文本框发生变化前触发

- keyup
    - 用户释放键盘上的键
    - 文本框已经发生变化后触发

### 文本事件

- textInput
    - 对 keypress 的补充
    - 用于将文本显示给用户前拦截文本，在文本插入文本框前触发
    - 这个事件更加关注的是字符，因此这个事件的 `event` 中包含 `data` 属性，其值为用户输入的字符的字面值

另外 `event` 上还有 `inputMethod` 属性，用于确定文本是如何输入到控件中的，如：键盘输入，粘贴输入，拖放输入等

### 键码

- 在 keydown 和 keyup 事件中，`event` 中包含 `keyCode`
- 对于数字，字母，字符键，`event.keyCode` 与 ASCII 码相同，大写的字母的 `keyCode` 值，与 Shift 键无关

### 字符编码

- 在 keypress 事件中，`event` 中包含 `charCode` 属性，只有在 keypress 事件中才有值，其值也是跟字符的 ASCII 值相同
- 对于需要跨浏览器，最好先判断 `charCode` 是否可用，不可用再使用 `keyCode`

## 合成事件

- 指的是使用输入发输入的文字
- 可以监听使用输入法时若干阶段的事件，如切换输入法，在输入法中输入合成字符的过程


