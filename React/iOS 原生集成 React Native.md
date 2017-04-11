# iOS 原生集成 React Native

[TOC]

## 确保安装了 Cocoapods

## 移动项目文件

项目文件目录最好调整如下，否则使用命令行方式运行的时候可能会报错，目测是因为命令会寻找 ios 文件夹的原因，个别文件可能在以后的步骤中生成

Project

- index.ios.js
- package.json
- node_modules/ (等下会自动生成)
- ios/ (存放 iOS 的项目文件)

## 创建 package.json

在根目录下，创建 package.json 文件，用于下载依赖

package.json 的内容一般如下：

```json
{
  "name": "EChartsTest",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
  "dependencies": {
    "native-echarts": "^0.3.0",
    "react": "16.0.0-alpha.6",
    "react-native": "0.43.0"
  }
}
```

name 一般为 项目的名字
dependencies 中，注意 react 与 react-native 的版本，尽可能使用 latest 版吧
使用 `npm info react` 与 `npm info react-native` 查看版本信息

## 安装依赖

运行命令 `npm install`
生成 node_modules 文件夹

## 创建 Podfile

**在 Cocoapods 安装之后再使用**

### 新建 Podfile

1. 进入到 iOS 的项目文件夹中
2. 运行命令 `pod init`，自动生成 Podfile 在当前目录

### 编辑 Podfile

```ruby
# The target name is most likely the name of your project.
target '<项目名称>' do

  # Your 'node_modules' directory is probably in the root of your project,
  # but if not, adjust the `:path` accordingly
  pod 'React', :path => '../../node_modules/react-native', :subspecs => [
    'Core',
    'RCTText',
    'RCTNetwork',
    'RCTWebSocket', # needed for debugging
    # Add any other subspecs you want to use in your project
  ]
  pod "Yoga", :path => "../../node_modules/react-native/ReactCommon/yoga"

end
```

#### :path

它的值应该为路径，路径从 Podfile 所在位置开始，一直回溯到 node_modules 中，所以，这里要看情况打点(../../)

####:subspecs

- 决定集成 React Native Framework 中的哪些部分
- 支持的 subspecs 位于 /node/modules/react-native/React.podspec
- subspec 一般根据功能来命名，如：
	- Core -> AppRegister, StyleSheet, View 等其他
	- RCTText -> <Text>

#### 注意

在 package.json 中，若 react-native 的版本选择了 0.42.0 或者更高的版本，则需要 pod "Yoga"，否则，会在 `pod install` 的时候会出现错误

> react native None of your spec sources contain a spec satisfying the dependency

```ruby
pod "Yoga", :path => "../../node_modules/react-native/ReactCommon/yoga"
```

### 安装 pod

在 Podfile 所在的文件夹中，运行命令：`pod install`

#### 注意

有时候会出现如下的 warning

> npm WARN react-native@0.43.0 requires a peer of react@16.0.0-alpha.6 but none was installed

就需要注意下了，需要重新安装一下对应的版本，否则可能在运行的时候出错


## 创建 index.ios.js

在根目录下，创建 React Native 入口文件 index.ios.js

```js
'use strict';

import React from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';
import Echarts from 'native-echarts';

const styles = StyleSheet.create({
    chartCenter: {
        flex:1,
        flexDirection:'row',
        alignItems:'center',
        justifyContent:'center'
    },
});

class TestView extends React.Component {
    render() {
        const option = {
            title: {
                text: 'ECharts demo'
            },
            tooltip: {},
            legend: {
                data:['销量']
            },
            xAxis: {
                data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
            },
            yAxis: {},
            series: [{
                name: '销量',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20]
            }]
        };
        return (
            <View style={styles.chartCenter}>
                <Echarts option={option} height={300} />
            </View>
        );
    }
}

AppRegistry.registerComponent('TestView', () => TestView);
```

## 原生接入

假设现处于 ViewController，跳转到 React Native 写的页面

在 ViewController.m 文件中，引入文件

```objc
#import <React/RCTRootView.h>
```

在跳转的地方，接入代码

```objc
NSURL *rnCodeEntry = [NSURL URLWithString:@"http://localhost:8081/index.ios.bundle?platform=ios"];
RCTRootView *rnRootView = [[RCTRootView alloc] initWithBundleURL:rnCodeEntry moduleName:@"TestView" initialProperties:nil launchOptions:nil];
    
UIViewController *vc = [[UIViewController alloc] init];
vc.view = rnRootView;
[self presentViewController:vc animated:YES completion:nil];
```

moduleName 在 index.ios.js 通过 `AppRegistry.registerComponent` 注册的组件名称

### 修改 App Transport Security

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>localhost</key>
        <dict>
            <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
            <true/>
        </dict>
    </dict>
</dict>
```

可以通过修改 plist 源文件进行修改操作；也可以通过 plist 的可视化界面配置，但配置的时候需要注意检查新建字段的初始类型

## 运行 

1. 转到 node_modules 的所在的目录，运行命令

	```shell
	npm start
	```

2. 运行项目，可通过 Xcode 进行，也可以通过命令行的方式

	```shell
	react-native run-ios
	```
	
	在我试验运行的时候，如果 iOS 的项目文件没有组织在 ios/ 目录下的话，	会出现错误，运行不起来

