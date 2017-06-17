# 使用 md5sum 生成密码

- [md5sum](#md5sum)
- [MD5](#md5)
- [生成密码](#生成密码)
	- [安装 md5sh1sum](#安装-md5sh1sum)
	- [生成密码](#生成密码)
- [References](#references)

## md5sum

用来计算和校验文件报文摘要的 **工具程序**，可以制作 md5 码和 md5 校验

## MD5

报文摘要算法 Message-Digest Algorithm 5

对任意长度的信息逐位进行计算，产生一个二进制长度为 128 位的指纹(摘要)

## 生成密码

设备为 Mac

### 安装 md5sh1sum

Linux 上是默认安装了 md5sh1sum 这个工具，但是 Mac 上并没有，因此需要先手动安装，这里通过 Homebrew 安装

```shell
brew install md5sh1sum
```

### 生成密码

命令格式为

```shell
echo <key> + <domainname> | md5sum | cut -c 1-8
```

其中：

- key 是一个自定义的字符串，可以说是密钥吧
- domainname 是网站的域名，其实 <key> + <domainname> 都是一个自定义的字符串，实际上可以换成其他任何字符串，只要能记住
- md5sum 就是刚才安装的工具里面用来计算 md5 值的
- cut 用来截取字符串，选项 `-c` 用来指定特定的字符位置，`1-8` 表明了截取字符串的 1-8 位

#### 事例

运行脚本

```shell
echo test + google.com | md5sum | cut -c 1-10
```

结果

```
bfa6b4f847
```

## References

- [http://weibo.com/1400854834/F8cC4xBCp?type=comment#_rnd1497680088544](http://weibo.com/1400854834/F8cC4xBCp?type=comment#_rnd1497680088544)

