# 不使用 IDE 运行 Java 程序

## 创建一个 Java 文件

创建 HelloWorld.java

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("hello world");
    }
}
```

## 将 Java 源代码编译成 class 文件

class 文件是机器可执行的文件

```sh
javac HelloWorld.java
```

## 运行 Java 程序

```sh
java HelloWorld
```

- 并不需要添加 `.class` 后缀名


