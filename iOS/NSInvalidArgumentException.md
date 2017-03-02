# NSInvalidArgumentException

### Terminating app due to uncaught exception 'NSInvalidArgumentException'

- [解决方法链接](http://www.isan.io/blog/swift-code-today-extension-bug)
- `reason: '*** setObjectForKey ***: object cannot be nil '`
- 使用 Swift 纯代码开发 Today Widget 时遇到
- 需要
	- 删除 Storyboard 文件
	- `NSExtensionMainStoryboard` 替换为 `NSExtensionPrincipalClass`，并将值设为 `<Widget extension name>.<View controller name>`

