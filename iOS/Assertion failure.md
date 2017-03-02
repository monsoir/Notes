# Assertion failure

### `*** Assertion failure in -[UITableView _endCellAnimationsWithContext:]`

> What is happening is that that tableview you are adding the new cell in is still not aware of the changes done to the self.fileList array. The order in which UIKit will add the new cell and check for the section count is unfortunately out of your control. However in order to fix this problem UItableview insert and delete operations need to be wrapped on beginsUpdate and endsUpdate methods call

```objc
[self.tableView beginUpdates];

// Do all the insertRowAtIndexPath and all the changes to the data source array

[self.tableView endUpdates];
```

