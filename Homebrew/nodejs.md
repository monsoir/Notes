# node.js

## 安装多版本 node

已存在就版本 node, 安装新版本 node

- 先解除对当前使用的 node 的连接
	- `brew unlink node`
- 运行安装命令，自动安装最新版
	- `brew install node`


## 使用 brew 安装多个版本的 node.js 后，切换不同版本的 node.js

- 查看已安装的 node 版本 
	- `ls /usr/local/Cellar/node*`

- 断开与现版本的 node.js 的链接，并切换到另一个 node.js 版本 
	- `brew unlink node && brew switch node <版本号，如 7.7.3>`


