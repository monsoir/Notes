# Debug

### 控制台打印视图层级结构

```cmd
po [<view> recursiveDescription]
```

### Not able to use debug view hierachy

- 问题

	UITextView 无法在视图 Debug 中显示
	
	```cmd
	Assertion failure in -[UITextView _firstBaselineOffsetFromTop]
	```

- Solution
	- [Link](http://stackoverflow.com/questions/37068231/assertion-failure-in-uitextview-firstbaselineoffsetfromtop)
	
	```objc
	// It is an issue of UITextView.
	// Add a category of UITextView and add the lines below to the .m file.
	// Only import when debug mode.
	
	- (void)_firstBaselineOffsetFromTop {
	}

	- (void)_baselineOffsetFromBottom {
	}

	```

### 添加 Autolayout Breakpoints

#### 问题
	
当视图的约束出现错误的时候，控制台在出错约束的最后会提示

```
Make a symbolic breakpoint at UIViewAlertForUnsatisfiableConstraints to catch this in the debugger.
The methods in the UIConstraintBasedLayoutDebugging category on UIView listed in <UIKit/UIView.h> may also be helpful.
```

---

#### 添加 Autolayout 断点的方法

1. 按下 `⌘ + 7`，转到 Show the Breakpoint navigator
2. 右下角添加 Symbolic Breakpoint...

	![](https://ws4.sinaimg.cn/large/006tNc79ly1fgba26ghu7j30cc07e0tg.jpg)

3. 在弹出的对话框中填入配置

	Action 的 Debugger Command
	
	- 对于 Swift 项目，填入 `expr -l objc++ -O -- [[UIWindow keyWindow] _autolayoutTrace]`
	- 对于 Objective-C 项目，填入 `po [[UIWindow keyWindow] _autolayoutTrace]` 

	![](https://ws2.sinaimg.cn/large/006tNc79ly1fgba3v3q6kj30vi0dcdi8.jpg)


4. 在运行时，当遇到有异常的约束时，会自动停下，并可以实时改变控件的属性，如

	```lldb
	(lldb) expr ((UIView *)0x7f9ea3d43410).backgroundColor = [UIColor redColor]
	```


