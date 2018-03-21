# 使用 maven 创建项目

## 安装 maven

Mac 下安装 maven

```sh
brew install maven
```

安装完成测试

```sh
mvn -version
```

## 使用 maven 创建项目

```sh
mvn archetype:generate -DgroupId=com.company.bank -DartifactId=consumerBanking -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

- 使用以上命令可以创建一个 maven 项目
- 一个项目是由 `groupId.artifactId.version` 来确定的
    - `groupId` 项目组
    - `artifactId` 项目本身
    - `version` 项目版本
- `DarchetypeArtifactId` 创建项目的模版

然而，以上命令可能出现的情况是，卡在了

```
generating project in Batch mode
```

通过添加选项 `-DarchetypeCatalog=local` 来让 maven 读取本地的配置文件

这是由于 maven 试图通过请求远程仓库文件受阻造成的，其实很有可能，这个文件已经在早已存在了 `~/.m2/` 中

于是，最后成功创建项目的命令是

```sh
mvn archetype:generate -DgroupId=com.company.bank -DartifactId=consumerBanking -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false -DarchetypeCatalog=local
```

