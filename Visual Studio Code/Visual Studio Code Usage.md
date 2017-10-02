# Visual Studio Code 的一些用法

- [通过命令行打开 Visual Studio Code](#通过命令行打开-visual-studio-code)
- [React Native](#react-native)
- [Side bar 中隐藏部分文件的显示](#side-bar-中隐藏部分文件的显示)
- [Python](#python)

## 通过命令行打开 Visual Studio Code

### 安装
1. 在 VSC 中通过 Command Palette (⇧⌘P) 输入 `shell command`
2. 寻找到 `Install 'code' command in PATH`

### 用法
1. 在新的 Terminal 中，命令 `code <file path>`

### 用处
- 使用 `code <file path>` 方式打开的 VSC 会将 Terminal 中的环境带到 VSC 中
	- 比如：在 Python 的 Virtual Environment 中打开 VSC，便会将当前 Python 的执行环境带到 VSC 中


## React Native

### 开启／关闭 React-packager

⌘ + ⇧ + P -> react native: start packager / react native stop packager

### 运行在 iOS 上

⌘ + ⇧ + P -> react native: Run iOS


## Side bar 中隐藏部分文件的显示

在 User Settings 中添加字段:

```js
"files.exclude": {
   "**/.git": true,
   "**/.svn": true,
   "**/.hg": true,
   "**/CVS": true,
   "**/.DS_Store": true,
   // 以上是默认的
   // 以下是自己添加的
   "*_*": true
}
```

## Python

### 配置 Python 的执行路径

在项目的 launch.json 中配置

```json
{
    "python.pythonPath": "/home/xxx/dev/ala/venv/bin/python"
}
```

参考自 [👉 https://github.com/DonJayamanne/pythonVSCode/wiki/Python-Path-and-Version#virtual-environments](#https://github.com/DonJayamanne/pythonVSCode/wiki/Python-Path-and-Version#virtual-environments)

## tab 切换快捷键设置

在 keybinding.json 中添加设置

### 在 tab group 中切换 tab

```json
{"key":"cmd+0", "command":"workbench.action.openLastEditorInGroup"},
{"key":"cmd+1", "command":"workbench.action.openEditorAtIndex1"}, 
{"key":"cmd+2", "command":"workbench.action.openEditorAtIndex2"}, 
{"key":"cmd+3", "command":"workbench.action.openEditorAtIndex3"},
{"key":"cmd+4", "command":"workbench.action.openEditorAtIndex4"}, 
{"key":"cmd+5", "command":"workbench.action.openEditorAtIndex5"}, 
{"key":"cmd+6", "command":"workbench.action.openEditorAtIndex6"},
{"key":"cmd+7", "command":"workbench.action.openEditorAtIndex7"},
{"key":"cmd+8", "command":"workbench.action.openEditorAtIndex8"}, 
{"key":"cmd+9", "command":"workbench.action.openEditorAtIndex9"}
```

### 切换 tab group

```json
{ "key": "cmd+ctrl+1", "command": "workbench.action.focusFirstEditorGroup" },
{ "key": "cmd+ctrl+2", "command": "workbench.action.focusSecondEditorGroup" },
{ "key": "cmd+ctrl+3", "command": "workbench.action.focusThirdEditorGroup" }
```


