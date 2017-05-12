# iOS 推送理解

- [iOS 推送基本原理](#ios-推送基本原理)
- [Provider, APNs, iOS, App 之间的工作关系](#provider-apns-ios-app-之间的工作关系)
- [推送消息打包格式](#推送消息打包格式)
- [用户查看推送消息](#用户查看推送消息)
- [Reference](#reference)

## iOS 推送基本原理

![](http://ww4.sinaimg.cn/large/006tNbRwly1ffimsxrt01j30i402iq33.jpg)

角色：

- Provider: 应用的后台推送服务器
- APNs: Apple Push Notification service 苹果自家的推送服务

无论怎样，在 iOS 上，所有远程推送到 iOS 设备的消息，都必须通过 APNs

因此，推送消息的基本过程必须是：

```
App 的后台推送服务器 -> APNs -> iOS -> App
```

于是，问题又来了：Provider, APNs, iOS, App 之间的内在关系又是什么？

## Provider, APNs, iOS, App 之间的工作关系

### 建立连接

![](http://ww3.sinaimg.cn/large/006tNc79gy1ffj0c9hasuj30fy09f3z2.jpg)

#### iOS 与 APNs

1. iOS (其实是设备)联网后，会与 APNs 建立并保持一个基于 TCP 协议的长连接 (Step 1)
2. 长连接连接成功后，iOS 向 APNs 发送消息，其中包含了设备序列号(UUID) (Step 1)
3. APNs 基于这个 UUID（可能还有其他东西吧），生成一个 deviceToken，加密后返回给 iOS (Step 2 + 3)

#### App 与 Provider

1. 当应用启动，并注册消息推送时，App 从 iOS 中获取 deviceToken(那个从 APNs 获取回来的东西)
2. 随后 App 将 deviceToken 传送到 Provider 处


### 推送消息

![](http://ww2.sinaimg.cn/large/006tNc79gy1ffj0gm7p15j30hk0bzjsb.jpg)

#### iOS 与 APNs

1. iOS 联网后，与 APNs 建立并维持基于 TCP 协议的长连接，同时，会验证 deviceToken (Step 4 + 5 + 6)

#### Provider 与 APNs

1. 当 Provider 需要推送消息时，会把将要接收消息的 deviceToken(即定位设备) ，推送内容，按照 APNs 指定格式打包好，发送给 APNs (Step 7)

#### APNs 与 iOS(设备)

1. APNs 接收到 Provider 的推送消息后，按照 deviceToken 查找指定的设备  (Step 8)
2. 若设备已经和 APNs 建立了连接，则立即将消息推送到设备 (Step 9)
3. 若设备不在线，则当设备在下次连接到 APNs 后再推送，Apple 不保证推送一定成功 (Step 9)

#### iOS 与 App

1. 收到 APNs 的推送后，iOS 会根据推送证书的内容，判断推送是发给哪个 App
2. 同时，iOS 根据用户的设置来选择推送呈现的方式

### 其他

注意的是：

1. App 获取 deviceToken，并上传到 Provider
2. Provider 发送推送消息到 APNs

这两个操作，都需要 **推送证书授权**

## 推送消息打包格式

![](http://ww1.sinaimg.cn/large/006tNc79ly1ffioxpsflmj30ei033jri.jpg)

信息包含了 5 个部分，其中，消息的主体部分称为 Payload：

1. 命令提示符
2. deviceToken 的长度
3. deviceToken值
4. Payload 的长度
5. Payload 主体，是一个 JSON 字典，最大值为 256，超越限制，APNs 将拒绝转发

信息包的例子：

```js
{
	"aps": {
		"alert": "显示的推送消息",
		"badge": 1, // App icon 上显示的消息数量
		"sound": "default" // 提示音
	}
}
```

若需要在 payload 中添加自定义数据，需要添加在 `aps`外。如用户查看推送的时候，要求打开 App 的某个页面，则可以：

```js
{
	"aps": {
		"alert": "显示的推送消息",
		"badge": 1, // App icon 上显示的消息数量
		"sound": "default" // 提示音
	},
	"page": "需要打开的页面值"
}
```

**必要时，通过缩写字段名称来减少 Payload 的大小**

## 用户查看推送消息

当用户点击推送消息时，iOS 根据推送证书来启动相应的 App

若 App 已经启动，则从这里获取 Payload

```objc
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
	// userInfo == Payload
}
```

若 App 尚未启动，可从启动时获取 Payload

```js
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	// 获取 Payload
	NSDictionary* userInfo = [launchOptions objectForKey:UIApplicationLaunchOptionsRemoteNotificationKey];
}
```

**这里只求对推送有个大概的了解，所以忽略了其他与推送有关的方法，如获取 deviceToken 和注册功能的方法**

## Reference

- [http://blog.tingyun.com/web/article/detail/571](http://blog.tingyun.com/web/article/detail/571)
- [http://tanqisen.github.io/blog/2013/02/27/ios-push-apns/](http://tanqisen.github.io/blog/2013/02/27/ios-push-apns/)

