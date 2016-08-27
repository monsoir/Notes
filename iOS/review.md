### `*** Assertion failure in -[UITableView _endCellAnimationsWithContext:]`

> What is happening is that that tableview you are adding the new cell in is still not aware of the changes done to the self.fileList array. The order in which UIKit will add the new cell and check for the section count is unfortunately out of your control. However in order to fix this problem UItableview insert and delete operations need to be wrapped on beginsUpdate and endsUpdate methods call

```objc
[self.tableView beginUpdates];

// Do all the insertRowAtIndexPath and all the changes to the data source array

[self.tableView endUpdates];
```


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

### `Attempting to load the view of a view controller while it is deallocating is not allowed and may result in undefined behavior (<UIAlertController`

- The issue will happen when the alert controller is created twice (don't know why).
- Solution
	- Before create the alert controller, first detect if a alert controller has shown on the screen, using:

		```objc
		if (!<The view controller that the alert controller is presenting on>.presentedViewController) {
			// Create the alert view controller.
			// Do something with the alet view controller.
		}
		```


