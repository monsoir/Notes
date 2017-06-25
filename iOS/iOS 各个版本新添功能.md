# iOS 各个版本新添功能

## iOS 7

### 界面

- 界面扁平化

### Dev

- UIKit 力学模型
- 多任务强化
	- 在后台进行内容下载
	- 通过推送在后台进行下载
- AirDrop

## iOS 8

### Dev

- 应用扩展 Extention
	- 可在应用的 Action 中添加自己的条目，在分享操作中添加自己的条目
- Handoff
- CoreLocation 可以进行楼层定位
- Touch ID API 开放验证（Touch ID 是从 iPhone 5s 开始有）
- 新增 Photos.framework

## iOS 9

### Dev

- iPad 分屏任务
- App Thining，针对设备的特点，下载相关的资源，如图片尺寸
- Bitcode，LLVM 的中间码，开发者提交 Bitcode 给 Apple, Apple 通过对提交的 Bitcode 进行优化

## iOS 10

### Dev

- 新增 SiriKit，包含
	- Intents.framework 表示 Siri 获取并解析的结果
	- IntentsUI.framework Siri 对操作的响应反馈

## iOS 11

### 界面

- Navigation title 字体放大 `prefersLargeTitles`

### Dev

- CoreML 机器学习
- ARKit 
- 拖拽操作
- 新增 FileProvider, FileProviderUI
- 不再支持 32bit 的 app
- 新增 DeviceCheck，生成设备的唯一 token，大小为 2bit
- 新增 PDFKit
- 新增 IdentityLookup，可以拦截系统 SMS 和 MMS 的消息，可做过滤功能
- 新增 Core NFC (iPhone 7, iPhone 7 Plus)+
- Auto Fill 自动填充密码，权限开放给开发者


