# Hammerspoon scripts

## 窗口布局管理

```lua
-- 屏幕左半部分
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Left", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.x = max.x
  f.y = max.y
  f.w = max.w / 2
  f.h = max.h
  win:setFrame(f, 0)
end)

-- 屏幕右半部分
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Right", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w / 2
  f.h = max.h
  f.x = max.w / 2
  f.y = max.y
  win:setFrame(f, 0)
end)

-- 屏幕上半部分
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Up", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w
  f.h = max.h / 2
  f.x = max.x
  f.y = max.y
  win:setFrame(f, 0)
end)

-- 屏幕下半部分
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Down", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w
  f.h = max.h / 2
  f.x = max.x
  f.y = max.h / 2
  win:setFrame(f, 0)
end)

-- 屏幕左下半部分
hs.hotkey.bind({"shift", "alt", "ctrl"}, "Left", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w / 2
  f.h = max.h / 2
  f.x = max.x
  f.y = max.h / 2
  win:setFrame(f, 0)
end)

-- 屏幕左上半部分
hs.hotkey.bind({"shift", "alt", "ctrl"}, "Up", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w / 2
  f.h = max.h / 2
  f.x = max.x
  f.y = max.y
  win:setFrame(f, 0)
end)

-- 屏幕右下半部分
hs.hotkey.bind({"shift", "alt", "ctrl"}, "Down", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w / 2
  f.h = max.h / 2
  f.x = max.w / 2
  f.y = max.h / 2
  win:setFrame(f, 0)
end)

-- 屏幕右上半部分
hs.hotkey.bind({"shift", "alt", "ctrl"}, "Right", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w / 2
  f.h = max.h / 2
  f.x = max.w / 2
  f.y = max.y
  win:setFrame(f, 0)
end)

-- 屏幕全屏，保留 menu bar
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "M", function()
  local win = hs.window.focusedWindow()
  win:maximize(0)
end)

-- 居中
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "C", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.w = max.w * 0.75
  f.h = max.h * 0.75
  f.x = max.x + max.w * (0.25 / 2)
  f.y = max.y + max.h * (0.25 / 2)
  win:setFrame(f, 0)

end)
```



