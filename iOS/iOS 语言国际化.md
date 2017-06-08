# iOS 语言国际化

- [配置需要国际化的语言](#配置需要国际化的语言)
- [系统设置国际化](#系统设置国际化)
- [代码中字符串的国际化](#代码中字符串的国际化)

## 配置需要国际化的语言

这是所有国际化操作的第一步，只需要执行一次

1. PROJECT -> Info -> Localizations

	![](https://ws3.sinaimg.cn/large/006tNbRwly1fgdtkliptqj31i80uc44h.jpg)

2. 确定语言国际化的内容

	若需要添加其他语言，则重复着两个步骤

	![](https://ws2.sinaimg.cn/large/006tNbRwly1fgdtpjdr4zj314a0nmacl.jpg)

## 系统设置国际化

关于一些系统设置的国际化，如：应用名称，隐私权限请求的描述文字等

1. 创建 Strings File

	选中 Info.plist(目的是在 Info.plist 所在的文件夹中创建文件) -> 添加文件，文件类型为 Strings File -> 文件命名为 InfoPlist(必须为此名字)
	
	创建完成后得到 InfoPlist.strings 文件

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fgdtyi5bfnj313i0seq7a.jpg)

2. 选择需要国际化的语言

	选中 📃InfoPlist.strings -> 打开 File inspector(⌥ + ⌘ + 0) -> 选中 Localize...

	![](https://ws1.sinaimg.cn/large/006tNbRwgy1fgdu62b3atj30e40lqgns.jpg)

	选中要国际化的语言进行 Localize

	![](https://ws3.sinaimg.cn/large/006tNbRwly1fgdua1mksqj30nm0a0dh6.jpg)

	上面步骤过后，后续只需要把需要国际化的语言勾上
	
	![](https://ws2.sinaimg.cn/large/006tNbRwly1fgdublvjfej30ea03wmxc.jpg)

	最后，在 project navigator 中可以看到 InfoPlist.strings 的结构变为
	dd
	![](https://ws3.sinaimg.cn/large/006tNbRwly1fgduepws6kj30g803g74l.jpg)

	最后的最后，在各自的国际化文件中填上对应语言的文字，如
	
	```
	CFBundleDisplayName = "Localizable App Name";
	```
	
	```
	CFBundleDisplayName = "国际化App名称";
	```

## 代码中字符串的国际化

基本步骤同上，但 **文件名称必须为 Localizable.strings**

使用方法

```
"localizedString" = "localized string";
```

```
"localizedString" = "本地化字符串";
```

```swift
NSLocalizedString("localizedString", comment: "")
```

