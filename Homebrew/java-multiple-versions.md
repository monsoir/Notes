# 多版本 Java 共存与切换

机主机子上安装的是 Java 9, 现在再安装 Java 8, 并实现 Java 8 与 Java 9 共存并方便地切换

## 安装 Java 8

```sh
brew tap caskroom/versions
brew cask install java8
```

## 查看机子上可用的 Java 版本

```sh
/usr/libexec/java_home -V
```

输出结果事例

```
Matching Java Virtual Machines (2):
    9, x86_64:  "Java SE 9" /Library/Java/JavaVirtualMachines/jdk-9.jdk/Contents/Home
    1.8.0_162, x86_64:  "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk-9.jdk/Contents/Home
```

## 自定义切换 Java 版本命令

在 ~/.bash_profile 中添加自定义命令

```sh
export JAVA_HOME_8=/Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home
export JAVA_HOME_9=/Library/Java/JavaVirtualMachines/jdk-9.jdk/Contents/Home
alias jdk8='export JAVA_HOME=$JAVA_HOME_8'
alias jdk9='export JAVA_HOME=$JAVA_HOME_9'
```

---

最后，每次打开终端时，默认的 Java 版本是 9, 可以通过输入命令 `jdk8` 来改变当前终端的 Java 版本

