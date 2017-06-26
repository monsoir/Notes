# iOS 中项目组织文件了解

## Target

描述一个 product/binary 是如何构建的

- build settings
	- compiler
	- linker flags

	
可以覆盖 Project 中的默认构建设置

## Project

- 包含多个 Targets
- 包含了每一个 Tatget 中的文件
- 定义了构建时的默认设置

## Workspace

包含了多个 Projects 并对它们进行管理

