# 结束一个线程

结束一个进程，推荐的操作是让线程自然结束，而不是在执行过程中中止，因为这样可能会有内存泄漏的风险，或者其他资源回收不恰当的问题

当真的需要一个对线程的中止操作时，合适的设计应该是：让线程去响应取消或中止的信息

稍微具体的做法便是

1. 设置一个标识符，用于记录是否需要中止线程，当线程接收到需要中止线程的信息时，设置该标志符
2. 线程任务间歇性暂停，检查标识符，看是否需要中止线程
3. 若需要中止线程，则进行资源清理并以恰当的方式退出；否则，继续执行一下轮的操作

来自官方文档的设计例子，对于接受中止信息，它是使用了 RunLoop 来监听一个 input source 来实现

```objc
- (void)threadMainRoutine
{
    BOOL moreWorkToDo = YES;
    BOOL exitNow = NO;
    NSRunLoop* runLoop = [NSRunLoop currentRunLoop];
 
    // Add the exitNow BOOL to the thread dictionary.
    NSMutableDictionary* threadDict = [[NSThread currentThread] threadDictionary];
    [threadDict setValue:[NSNumber numberWithBool:exitNow] forKey:@"ThreadShouldExitNow"];
 
    // Install an input source.
    [self myInstallCustomInputSource];
 
    while (moreWorkToDo && !exitNow)
    {
        // Do one chunk of a larger body of work here.
        // Change the value of the moreWorkToDo Boolean when done.
 
        // Run the run loop but timeout immediately if the input source isn't waiting to fire.
        [runLoop runUntilDate:[NSDate date]];
 
        // Check to see if an input source handler changed the exitNow value.
        exitNow = [[threadDict valueForKey:@"ThreadShouldExitNow"] boolValue];
    }
}
```

