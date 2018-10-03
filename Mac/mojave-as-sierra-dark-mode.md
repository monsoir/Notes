# Mojave 只实现 High Sierra 的暗色主题

只改变菜单栏与 Dock 栏的暗色主题

1. 重新选取主题颜色为 light    
2. 终端输入命令

    ```
    defaults write -g NSRequiresAquaSystemAppearance -bool Yes
    ```

3. 重新登陆用户账号
4. 偏好设置选取主题颜色为 Dark

撤销

与上述步骤一致，但输入命令为

```
defaults delete -g NSRequiresAquaSystemAppearance
```

## Referreces

- [How to Use Only a Dark Menu Bar and Dock in macOS Mojave](https://www.tekrevue.com/tip/only-dark-menu-bar-dock-mojave/)

