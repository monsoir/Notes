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

