# 在 Queue 中共享数据

如果想在队列中存放一些共享的数据，那么，可以将这部分数据存放在一个 context (上下文)中，而系统是不会用到这些自定义的数据

- 调用 `dispatch_set_context` 函数设置 context
- 调用 `dispatch_get_context` 函数获取 context

## 清除数据

在上面定义了共享数据，当我们需要清除这部分数据时，需要使用到 `dispatch_set_finalizer_f` 函数

```objc
void myFinalizerFunction(void *context)
{
    MyDataContext* theData = (MyDataContext*)context;
 
    // Clean up the contents of the structure
    myCleanUpDataContextFunction(theData);
 
    // Now release the structure itself.
    free(theData);
}
 
dispatch_queue_t createMyQueue()
{
    MyDataContext*  data = (MyDataContext*) malloc(sizeof(MyDataContext));
    myInitializeDataContextFunction(data);
 
    // Create the queue and set the context data.
    dispatch_queue_t serialQueue = dispatch_queue_create("com.example.CriticalTaskQueue", NULL);
    dispatch_set_context(serialQueue, data);
    dispatch_set_finalizer_f(serialQueue, &myFinalizerFunction);
 
    return serialQueue;
}
```


