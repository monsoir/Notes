# Debug

### Print the hierarchy of view

```cmd
po [<view> recursiveDescription]
```

### Not able to use debug view hierachy

- Problem
	
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

