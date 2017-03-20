# Swift 项目中 引用 OC 源码（以 FMDB为例）
## 并且运用于 Today Widget 中

- [FMDB 地址](https://github.com/ccgus/fmdb)
- 在项目中建立一个文件夹，便于代码管理，假定为 `fmdb`
- 将 FMDB 中的 src 下的源代码，即 .h 与 .m 文件拖入到工程中的对应文件夹下
	- 注意是将 .h 与 .m 文件拖入，而不是将 fmdb 的文件夹拖入，否则 Xcode 不会提示创建 `xxx-Bridge-Header.h` 文件
- 创建 `xxx-Bridge-Header.h` 后，在此文件中，添加 `#import "FMDB.h"`
- 假定已经创建了一个 Cocoa Touch Framework（假定为 xxxKit），用于共享 App 与 Today Widget 的共用源代码
- 在 xxxKit 中的 Linked Frameworks and Libraries 中添加 `libsqlite3.0.tdb`
- 在 xxxKit 中的 Build Phases -> Complie Sources 中添加 FMDB 的 .m 文件 
- 在 App 和 Today Widget 的 Target 配置中引入 xxxKit

-----

经过实践，发现上面的方法忽略了一个重要的细节。由于创建了一个 framework 用于放置共享文件，在 Today Widget 文件中使用 FMDB 的时候将会报错：没有找到模块(non-modular-header-inside-framework-module)

正确方法

1. 删除之前添加到项目中的 FMDB 相关文件，以及桥接文件 
2. 从下载的 FMDB 的源代码中，全部加入到创建的 framework 的文件夹中
3. umbrella file，文件名字形如 <framwork name>.h，在其中添加需要使用到的文件，如 #import "FMDatabase.h"
4. 最后，依次点击添加到 umbrella file 中的头文件，并 option + command + 0 点开属性检查器，在 target relationship 中找到刚才创建的 framework，勾选并从下拉框中选择 Public

这一切的步骤，其实都在 FMDB 的文档说明的 Using FMDB in a framework 部分中有讲述

