# 创建 Operation 对象

## 创建 NSInvocationOperation 对象

比较适用的场合：任务的对象，执行的方法已经存在

```objc
@implementation MyCustomClass
- (NSOperation*)taskWithData:(id)data {
    NSInvocationOperation* theOp = [[NSInvocationOperation alloc] initWithTarget:self
                    selector:@selector(myTaskMethod:) object:data];
 
   return theOp;
}
 
// This is the method that does the actual work of the task.
- (void)myTaskMethod:(id)data {
    // Perform the task.
}
@end
```

上面已经存在了一个 `MyCustomClass` 及任务需要执行的方法 `myTaskMethod`, 那么，就可以使用 `NSInvocationOperation` 通过传参，直接创建出一个 operation

## 创建 NSBlockOperation 对象

比较使用的场合：执行的任务不需要入参和返回数据

```objc
NSBlockOperation* theOp = [NSBlockOperation blockOperationWithBlock: ^{
  NSLog(@"Beginning operation.\n");
  // Do some work.
}];
```

还有

- 在创建了 operation 对象后，还可以使用 `addExecutionBlock:` 方法来添加更多的 block 小任务
- 如果需要串行执行 operation 中的每个 block, 就需要 operation 提交到对应的 operation queue 中

