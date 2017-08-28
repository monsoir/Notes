# Mongodb

## 安装

```sh
brew install mongodb
```


## 设置数据写入文件

### 默认

1. 需要先创建文件

    ```sh
    mkdir -p /data/db
    ```

2. 赋予权限

    ```sh
    chown `id -u` /data/db
    ```

### 自定义

```sh
mongo -dbpath dir_name
```

## 开启服务

```sh
mongod
```

## 数据库控制命令

- show dbs 显示所有数据库



