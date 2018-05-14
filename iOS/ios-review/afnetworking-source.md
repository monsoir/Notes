# AFNetworking 源码阅读

## AFURLSessionManager

`AFURLSessionManager` 使用特定的 `NSURLSessionConfiguration` 来创建和管理 `NSURLSession`

`AFURLSessionManager` 同时实现了多个协议

- `NSURLSessionTaskDelegate`
- `NSURLSessionDataDelegate`
- `NSURLSessionDownloadDelegate`
- `NSURLSessionDelegate`

> 对于使用 HTTP 协议时，应该直接使用 `AFURLSessionManager` 子类 `AFHTTPSessionManager`
> 
> 若想对 `AFURLSessionManager` 的 HTTP 协议上的行为进行扩展，应该考虑继承 `AFHTTPSessionManager`

### 协议的实现方法

对于 `AFURLSessionManager` 实现了的协议中，已经实现了的方法

#### NSURLSessionDelegate

全实现了

- `URLSession:didBecomeInvalidWithError:` session 已作废
- `URLSession:didReceiveChallenge:completionHandler:` 请求需要 session-level 的验证信息
- `URLSessionDidFinishEventsForBackgroundURLSession:` session 中的所有消息都已经被发送出去了

#### NSURLSessionTaskDelegate

`NSURLSessionDataDelegate` 与 `NSURLSessionDownloadDelegate` 的「父协议」

- `URLSession:willPerformHTTPRedirection:newRequest:completionHandler:` 请求重定向
- `URLSession:task:didReceiveChallenge:completionHandler:` 请求需要验证信息
- `URLSession:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend:` 上传到服务器的数据进度，会周期性调用
- `URLSession:task:needNewBodyStream:` 服务器要求一个新的 request body stream，以下情况会被调用
    - 当请求由 `uploadTaskWithStreamedRequest:` 创建，用来提供初始的 request body stream
    - 当发生需要验证，或服务器发生了可恢复的错误时，提供替代前一个请求的 request body stream
    - 若 request body 的数据使用的是文件 URL 或一个 `NSData` 对象，则不需要实现此委托方法
- `URLSession:task:didCompleteWithError:`

__这个协议中的方法没有全部实现__

#### NSURLSessionDataDelegate

简单的数据请求任务或上传任务

- `URLSession:dataTask:didReceiveResponse:completionHandler:`
- `URLSession:dataTask:didBecomeDownloadTask:` 任务被改变为下载任务
- `URLSession:dataTask:didReceiveData:`
- `URLSession:dataTask:willCacheResponse:completionHandler:`

__这个协议中的方法没有全部实现__

- `URLSession:dataTask:didBecomeStreamTask:`

#### NSURLSessionDownloadDelegate

数据下载任务

- `URLSession:downloadTask:didFinishDownloadingToURL:`
- `URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesWritten:totalBytesExpectedToWrite:`
- `URLSession:downloadTask:didResumeAtOffset:expectedTotalBytes:`

### 暴露的 property

被管理的 session

```objc
@property (readonly, nonatomic, strong) NSURLSession *session;
```

任务队列，用于调用委托回调方法

```objc
@property (readonly, nonatomic, strong) NSOperationQueue *operationQueue;
```

响应序列化器，由 `dataTaskWithRequest:success:failure:` 和 GET/POST 等便利方法返回的响应，都会经由此序列化器处理，默认为 `AFJSONResponseSerializer`

```objc
@property (nonatomic, strong) id <AFURLResponseSerialization> responseSerializer;
```

对服务器的安全信任策略，默认使用 `defaultPolicy`

```objc
@property (nonatomic, strong) AFSecurityPolicy *securityPolicy;
```

网络状态检测器，`AFURLSessionManager` 默认使用 `[AFNetworkReachabilityManager sharedManager]`

```objc
@property (readwrite, nonatomic, strong) AFNetworkReachabilityManager *reachabilityManager;
```

---

#### 任务相关

当前 session 中在运行的任务，包括 data, upload, download

```objc
@property (readonly, nonatomic, strong) NSArray <NSURLSessionTask *> *tasks;
```

当前 session 中在运行的 data 任务

```objc
@property (readonly, nonatomic, strong) NSArray <NSURLSessionDataTask *> *dataTasks;
```

当前 session 中在运行的 upload 任务

```objc
@property (readonly, nonatomic, strong) NSArray <NSURLSessionUploadTask *> *uploadTasks;
```

当前 session 中在运行的 download 任务

```objc
@property (readonly, nonatomic, strong) NSArray <NSURLSessionDownloadTask *> *downloadTasks;
```

---

#### 回调相关

`completionBlock` 使用的回调队列，若不指定，默认是主队列

```objc
@property (nonatomic, strong, nullable) dispatch_queue_t completionQueue;
```

`completionBlock` 使用的回调派遣组，若不置顶，默认是一个内置的派遣组

```objc
@property (nonatomic, strong, nullable) dispatch_group_t completionGroup;
```


