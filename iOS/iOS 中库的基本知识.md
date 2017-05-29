# 库

- [闭源库](#闭源库)
- [CPU 架构](#cpu-架构)
- [静态库的制作](#静态库的制作)
	- [Objective-C 项目](#objective-c-项目)
	- [Swift 项目](#swift-项目)
- [使用 framework](#使用-framework)
- [Demo 地址](#demo-地址)
- [Reference](#reference)

库有两大类

- 开源库
	- 公开源代码，可以看到具体实现
- 闭源库
	- 不公开源代码，不能看到具体实现，只能得到编译后的二进制文件

## 闭源库

闭源库有两类

- 静态库
- 动态库

### 静态库

静态库的存在形式

- .a
- .framework

链接时，静态库会被完整地复制到可执行文件中，被多次使用就有多份冗余拷贝

.a 是纯二进制文件，不能直接使用，要有 .h 配合
.framework 包含二进制文件外，还有资源文件，可以直接使用

### 动态库

动态库的存在形式

- .dylib
- .framework

链接时，不会复制，程序运行时又系统动态加载到内存，供程序调用。每个静态库，系统只会加载一次，供多个程序共用，节省内存

**项目中使用了自制的动态库，不允许上架**

--- 

系统的 framework 属于动态库，自制的 framework 属于静态库

## CPU 架构

模拟器：

CPU 架构 | 机型
-------- | ----
i386 | iPhone 4s, 5
x86_64 | iPhone 5s, 6, 6s, 6s Plus

---

真机

CPU 架构 | 机型
-------- | ----
armv7 | iPhone 3GS, 4s
armv7s | iPhone 5, 5c
arm64 | iPhone 5s, 6, 6s, 6s Plus

---

可以使用一下命令查看静态库支持的架构

```shell
lipo -info xxx.a
```

---

由于不同机型的 CPU 架构不同，要让一次编译支持多个架构的静态库，需要设置

```
Build Settings -> Build Active Architecture Only -> NO
```

## 静态库的制作

### Objective-C 项目

#### 制作 .a 静态库

**1. 创建一个库包装箱**

新建项目 -> Framework & Library -> Cocoa Touch Static Library

---

**2. 添加包装的源码，以及头文件**

选中 Target -> Build Phases -> Copy Files -> 添加暴露的头文件

---

**3. 可能需要引入静态库依赖的系统框架**

根据报错信息进行操作

---

**4. 设置静态库支持 CPU 的架构**

选中 Target -> Build Settings -> Architechtures -> Build Active Architechture Only

---

**5. 选择 release 模式**

#### 制作 .framework 静态库


**1. 创建一个库包装箱**

新建项目 -> Framework & Library -> Cocoa Touch Static Framework

---

**2. 添加包装的源码，以及头文件**

选中 Target -> Build Phases -> Headers -> 添加要暴露的头文件

---

**3. 设置静态库支持的 CPU 架构**

选中 Target -> Build Settings -> Architechtures -> Build Active Architechture Only

**4. 设置库的类型**

默认制作的是动态库，需要手动设置为静态库

选中 Target -> Build Settins -> 搜索 mach-o -> Linking Mach-O Type -> 修改值为 Static Library

若生成了动态库，则需要在使用这个动态库的工程中，在 Gneneral -> Embeded Binaries 中引入动态库

### Swift 项目

#### 创建 Framework 项目

使用 ⇧ + ⌘ + N 新建一个 Framework 项目

![](https://ws3.sinaimg.cn/large/006tNc79gy1ffstxpwmkaj30ke0ez0uc.jpg)

#### 添加打包代码

注意需要在暴露的 API 处使用 `public` 进行修饰，因为这是要在当前 module 之外是使用这些代码

#### 进行打包

##### 生成模拟器专用的 Framework

1. 运行目标选择任一模拟器

	![](https://ws3.sinaimg.cn/large/006tNc79gy1ffsu4azarmj3091011749.jpg)

2. 使用 ⇧ + ⌘ + I 进行 Profiling 编译

	> 直接使用 ⌘ + B 进行编译，会得到一个 Debug 版本的结果，若是用于调试的话，可以选择这样做

3. 找出生成的 Framework

	通过 Xcode 的菜单 Window -> Organizer 打开 Organizer

	![](https://ws1.sinaimg.cn/large/006tNc79gy1ffsu9iq7xlj30b407cq3h.jpg)

	在 Archives 选项卡处，选择任意一个 archive(需要之前有过 Archive 操作 )，右键 Show in Finder
	
	![](https://ws2.sinaimg.cn/large/006tNc79gy1ffsubq15jnj3090036wen.jpg)
	
	Finder 打开后，不断往上回溯文件路径，直到文件路径为 (...)/Xcode/Archives/
	
	进入 📁DerivedData，找到 Framework 的项目文件夹，假设为 A
	
	进入 A/Build/Products/Release-iphonesimulator/，当中便有 xxx.framwork，这个就是目标文件了

	> 若使用 ⌘ + B 的方式进行构建，则会存在 A/Build/Products/Debug-iphonesimulator/
	
	然后可以直接拷贝 xxx.framework 出来直接用了


##### 生成真机专用的 Framework

1. 运行目标选择 Generic iOS Device

2. 使用 ⇧ + ⌘ + I 进行 Profiling 编译

3. 找出生成的 Framework

	基本步骤与生成模拟器版本的 Framework 一样
	
	但 xxx.framework 的位置在 A/Build/Products/Release-iphoneos/xxx.framework

##### 合并模拟器和真机的 Framework，生成通用的二进制文件，使得开发时不用切换

1. 进入 A/Build/Products/

2. 运行命令

	```shell
	lipo -create -output xxx.framework \
								Release-iphoneos/xxx.framework/xxx \
								Release-iphonesimulator/xxx.framework/xxx
	```
	
	在 A/Build/Products 下，将生成一个文件
	
	![](https://ws1.sinaimg.cn/large/006tNc79gy1ffsx45k3fxj303702mjra.jpg)

3. 迁移相关文件
	
	将模拟器或者真机的 framework 复制一份出来，这里假设复制了模拟器的包，设为 B
	
	**将通用的二进制包放到 B 中，替换掉原本的模拟器的包**
	
	将 📁 A/Build/Products/Release-iphoneos/xxx.framework/Modules/xxx.swiftmodule 中的，扩展名为 swiftmodule 的文件名拷贝到 B 中
	
	最后 B 中应该会包含内容有：
	
	- i386.swiftmodule
	- x86_84.swiftmodule
	- arm.swiftmodule
	- arm64.swiftmodule
	- xxx (就是那个二进制文件)

	至此，framework 已经打包好

## 使用 framework

1. 直接将打包好的 framework 托放到项目中，记得勾选 Copy Items if needed
2. 设置好包依赖关系

	> 需要注意，要设置好 Embeded Binaries，添加好 xxx.framework，否则将会报错：Reason: image not found
	
## Demo 地址

[打包 framework 的示例，使用的是 Swift](https://github.com/Monsoir/AuthorizationKitDemo)

## Reference

[http://www.jianshu.com/p/f14553494d88](http://www.jianshu.com/p/f14553494d88)
[http://swifter.tips/code-framework/](http://swifter.tips/code-framework/)

