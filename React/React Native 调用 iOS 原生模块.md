# React Native 调用 iOS 原生模块

- [原生端工作](#原生端工作)
	- [创建一个类](#创建一个类)
	- [导出这个类为一个模块](#导出这个类为一个模块)
	- [导出 React Native 为调用的方法](#导出-react-native-为调用的方法)
	- [导出常量](#导出常量)
- [React Native 端工作](#react-native-端工作)
	- [在调用原生模块的文件中引入文件](#在调用原生模块的文件中引入文件)
	- [正常编写](#正常编写)
- [Reference](#reference)

## 原生端工作

### 创建一个类

创建一个类，假设为 A

在 A 中引入文件

```
#import <React/RCTBridgeModule.h>
```

同时，A 继承于 `NSObject`，实现协议 `RCTBridgeModule`

### 导出这个类为一个模块

在 A 的 @implementation 中，添加宏

```
RCT_EXPORT_MODULE();
```

> 这个宏也可以添加一个参数用来指定在Javascript中访问这个模块的名字。如果你不指定，默认就会使用这个Objective-C类的名字

### 导出 React Native 为调用的方法

声明要给Javascript导出的方法，否则React Native不会导出任何方法。声明通过RCT_EXPORT_METHOD()宏来实现

#### 导出方法有两种

使用 `RCT_EXPORT_METHOD` 宏，将方法包裹起来，这个宏使用回调方法来协作处理费时任务

```objc
RCT_EXPORT_METHOD(canEvaluatePolicy:(RCTResponseSenderBlock)callback) {
  
  NSError *error = nil;
  if (![YAuthenticator canEvaluatePolicyWithError:&error]) {
    if (error) {
      NSDictionary *jsError = RCTMakeError(error.userInfo[NSLocalizedDescriptionKey], nil, nil);
      callback(@[jsError, @[]]);
    }
  } else {
    callback(@[[NSNull null], @[]]);
  }
}
```

---

使用 `RCT_REMAP_METHOD` 宏，将方法包裹起来，这个宏使用 promise 来协作处理费时任务

```objc
RCT_REMAP_METHOD(findEvents,
                 resolver:(RCTPromiseResolveBlock)resolve
                 rejecter:(RCTPromiseRejectBlock)reject) {
  
  NSError *error = nil;
  if (![YAuthenticator canEvaluatePolicyWithError:&error]) {
    if (error) {
      reject(@(error.code).stringValue, @"", error);
    }
  } else {
    resolve(@[]);
  }
}
```

### 导出常量

实现实例方法

```objc
- (NSDictionary *)constantsToExport {
  // TouchIDUsageDescriptions 使用 Touch ID 的描述信息
  // TouchIDError 验证错误代码
  
  return @{@"TouchIDUsageDescriptions": @{
               @"fallbackTitle": @"fallbackTitle",
               @"reason": @"reason",
               @"shouldUseCustomizedFallbackButton": @"shouldUseCustomizedFallbackButton",
               @"shouldHideFallbackButton": @"shouldHideFallbackButton"
               },
           @"TouchIDError": @{
               @"touchIDAuthenticationFailed": @(LAErrorAuthenticationFailed),
               @"touchIDUserCancel": @(LAErrorUserCancel),
               @"touchIDUserFallback": @(LAErrorUserFallback),
               @"touchIDSystemCancel": @(LAErrorSystemCancel),
               @"touchIDPasscodeNotSet": @(LAErrorPasscodeNotSet),
               @"touchIDNotAvailable": @(LAErrorTouchIDNotAvailable),
               @"touchIDNotEnrolled": @(LAErrorTouchIDNotEnrolled),
               @"touchIDLockout": @(LAErrorTouchIDLockout),
               @"touchIDAppCancel": @(LAErrorAppCancel),
               @"touchIDInvalidContext": @(LAErrorInvalidContext)
               }
           };
}
```

## React Native 端工作

### 在调用原生模块的文件中引入文件

```js
import {
	...
	NativeModules,
} from 'react-native';
```

### 正常编写

```js
let touchIDManager = NativeModules.TouchIDManager;
// 定义 callback 之类的，当作参数传入到导出的方法去

let touchIDManager = NativeModules.TouchIDManager;
    let canEvaluateCallback = (error, events) => {
      console.log("find me");
      if (error) {
        console.error(error);
      } else {
        // 定义一些 Touch ID 的描述性文字
        let fallbackTitle = touchIDManager.TouchIDUsageDescriptions.fallbackTitle;
        let reason = touchIDManager.TouchIDUsageDescriptions.reason;
        let shouldUseCustomizedFallbackButton = touchIDManager.TouchIDUsageDescriptions.shouldUseCustomizedFallbackButton;
        let shouldHideFallbackButton = touchIDManager.TouchIDUsageDescriptions.shouldHideFallbackButton;
        let touchIDDescriptions = {
          fallbackTitle: "输入密码",
          reason: "test",
          shouldUseCustomizedFallbackButton: true,
          shouldHideFallbackButton: false,
        };

        // Touch ID 识别回调
        let callback = (error, events) => {
          console.log("find me 2");
          if (error) {
            console.error(error);
          } else {
            console.log("seems success");
          }
        };
        
        touchIDManager.evaluatePolicyWithDescriptions(touchIDDescriptions, callback);
      }
```


## References

[http://reactnative.cn/docs/0.45/native-modules-ios.html#content](http://reactnative.cn/docs/0.45/native-modules-ios.html#content)

