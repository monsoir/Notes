# graphviz

[👉官网地址](http://www.graphviz.org)

## 安装

```shell
brew install graphviz
```

## 例子

以下例子的流程图中，叙述了 Python 的 Django Rest Framework 中数据库, Model, Serializer, 格式为 JSON/XML 的数据之间的关系

效果图

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fg8fgyw61wj31kw0azaee.jpg)

代码：

```dot
digraph d {
 # 全局的一些设置
 resolution=400;
 rankdir="LR"; # 图的全局方向为 从左到右
 #splines=polyline; # 指定连线的样式

 # 定义流程图中的一些实体，实体的形状等都可以设置
 1 [label="Model", shape=box, fontsize=15]
 2 [label="DataBase", shape=box, fontsize=15]
 3 [label="Serializer", shape=box, fontsize=15]
 4 [label="JSON/XML", shape=oval, fontsize=15]

 # subgraph 创建一个子流程图，可以看作一个模块
 subgraph orm {
     rankdir="TB";

     1 -> 2 [xlabel="sync automatically", style=dashed, dir="both", fontsize=10, constraint=false]
     #2 -> 1 [style=dashed, arrowhead=none, fontsize=10, constraint=false]
 }

 subgraph flow {
     rankdir="LR";

     3 -> 4 [label="serializer.isValid()\n2.serializer.save()", fontsize=10]
     4 -> 3 [label="serializer.data", fontsize=10]
 }

 subgraph combine {
     rankdir="TB";

     1 -> 3 [label="1.serializer(data=(JSON/XML)) OR\n2.serializer(object, data=(JSON/XML))", fontsize=10]
     3 -> 1 [label="serializer(model)", fontsize=10]
 }

} 
```

### 记要

1. 实体，连线之间的属性设置，真的没有什么捷径，参照 [👉官方文档](http://www.graphviz.org/content/attrs) 逐个对照着找吧
2. 一些已知的属性区别
	- label, xlabel
		- label 位于连线的旁边（即连线的上面或下面）
		- xlabel 位于连线的中间，即文字的横中轴线大概与连线重合的那种
3. 实体的形状通过 shape 设置，边缘的样式通过 style 设置
4. 连线的样式通过 style 设置
5. constraint=false 可以设置忽略默认设置的层级关系
6. 通过 resolution 可以设置输出图片的分辨率和大小


## 生成图片

生成 png 图片

```shell
dot -T png -O chartDemo.dot
```

