# Attempting to

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

