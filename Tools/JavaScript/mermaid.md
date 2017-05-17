# mermaid

[GitHub 地址](https://github.com/knsv/mermaid)

[官网地址](http://knsv.github.io/mermaid/)

用来绘制流程图

- [安装环境](#安装环境)
- [生成流程图](#生成流程图)

## 安装环境

mermaid 依赖 node.js，因此需要先安装 node.js

mermaid 依赖 PhantomJS，因此需要安装 PhantomJS

这里采用本地安装的方法，而非全局安装

```shell
# 安装 PhantomJS
npm install -D phantomjs

# 安装 mermaid
npm install -D mermaid
```

--- 

安装完成后，即可以使用 mermaid 的规则编写流程图的代码，规则语法参考 [官方文档](http://knsv.github.io/mermaid/#graph)

## 生成流程图

流程图代码完成后，使用命令构建流程图

由于之前的环境是使用本地安装的方法，因此命令与全局安装的有所不同 

在 package.json 所在的文件夹中运行命令

```shell
./node_modules/.bin/mermaid <filename>
```

命令运行完成之后，即生成流程的 png 格式的图片，存放在与 filename 相同的目录中

mermaid 命令可以配置更多的选项，具体查看 [官方文档](http://knsv.github.io/mermaid/#configuration53)

```
-s --svg             Output SVG instead of PNG (experimental)
  -p --png             If SVG was selected, and you also want PNG, set this flag
  -o --outputDir       Directory to save files, will be created automatically, defaults to `cwd`
  -e --phantomPath     Specify the path to the phantomjs executable
  -c --sequenceConfig  Specify the path to the file with the configuration to be applied in the sequence diagram
  -h --help            Show this message
  -v --verbose         Show logging
  -w --width           width of the generated png (number)
  --version            Print version and quit
```

从上述的命令中，我们还可以看到

- 通过本地安装的 package，可以在 /node_modules/.bin 中获取到对应的可用命令
- filename 的文件格式没有具体限制，只要文件内容的语法规则符合就行

