# node.js

## 使用 brew 安装多个版本的 node.js 后，切换不同版本的 node.js

- 查看已安装的 node 版本 
	- `ls /usr/local/Cellar/node*`

- 断开与现版本的 node.js 的链接，并切换到另一个 node.js 版本 
	- `brew unlink node && brew switch node <版本号，如 7.7.3>`


