# Mac 下 Laravel 环境搭建

## 大致步骤

1. 安装 VirtualBox
2. 安装 Vagrant
3. 导入 Homestead Box
4. ~~安装 Git~~
5. 安装 Homestead 管理脚本
6. 配置 Homestead.yaml 文件
7. 启动 Homestead 虚拟机

## 安装 VirtualBox

[官网链接，通过 pkg 安装](https://www.virtualbox.org/wiki/Downloads)

## 安装 Vagrant

### 是什么

- 管理虚拟机
- 提供一个可配置，可移植，可复用的软件环境
- 通过编写 Vagrantfile 文件控制虚拟机的各种配置
- 多人合作时，同步 Vagrantfile 即可同步开发环境

### 安装

[安装 Vagrant, 通过 pkg 安装](https://www.vagrantup.com/downloads.html)

## 导入预配置的 Homestead Box

### Homestead 是什么

- Homestead 利用 Vagrantfile 提供的便利，定制了 Laravel 的开发环境
- Homestead 包含了
    - Homestead 管理脚本
    - Homestead Box 虚拟机盒子
- Homestead 虚拟机中包含了
    - Nginx Web 服务器
    - PHP 7.0
    - MySQL
    - Postgres SQL
    - Redis
    - Memcached
    - Node
    - ...

#### Homestead 管理脚本

- 对 Vagrantfile 文件进行定制
- 从 Homestead.yaml 文件中读取配置信息，解析为 Vagrant 命令后对虚拟机进行配置
- 常用配置
    - IP 配置，端口映射
    - Nginx Site 创建
    - 数据库创建
    - 主机文件夹挂在到虚拟机

#### Homestead Box 虚拟机盒子

- homestead.box 文件是虚拟机盒子，是 Vagrant Box 虚拟盒子
- 预装了上述的开发环境需要的工具

### 安装

下载 Homestead Box, 通过添加盒子的元数据文件来添加盒子

    ```sh
    vagrant box add metadata.json
    ```

## 安装 Homestead 管理脚本

下载 Homestead 管理脚本

[官方下载地址](https://github.com/laravel/homestead)
    
下载完成后，初始化 Homestead

```sh
# 进入到脚本的文件夹
bash init.sh
```

生成的 3 个文件

- `Homestead.yaml` 主配置文件
- `after.sh` 每次 Homestead 盒子重置后，调用的脚本
- `aliases` 每次 Homestead 盒子重置后，替换到虚拟机的 `~/.bash_aliases` 中

## 配置 Homestead.yaml 文件

Homestead.yaml 配置有

- 虚拟机设置
- SSH 密钥登录配置
- 共享文件夹配置
- 站点配置
- 数据库配置
- 自定义变量配置

### 虚拟机配置

```yaml
ip: "映射到虚拟机的 IP"
memeory: 2048 # 内存分配
cpus: 1 # 分配的 CPU 个数
provider: virtualbox # 虚拟机提供者
```

### SSH 密钥登录配置

```yaml
# SSH 的公钥文件，复用主机上的公钥文件
authorize: ~/.ssh/id_rsa.pub
```

若在虚拟机上使用了 git 之类的 ssh 功能，则将复用主机上的密钥文件，将公私钥复制到虚拟机上

```yaml
keys:
    - ~/.ssh/id_rsa
    - ~/.ssh/id_rsa.pub
```

### 共享文件夹配置

将主机的文件夹映射到 Homestead 虚拟机上的文件夹

```yaml
folders:
    - map: ~/Code
    - to: /home/vagrant/Code
```

### 站点配置

站点配置允许在主机中，通过域名访问虚拟机中的 Laravel 应用，需要修改主机的 hosts(`/etc/hosts`) 文件

```yaml
sites:
    - map: domains.com
    - to: /path/to/app/public
```

### 数据库配置

为 Homestead 指定数据库名称

```yaml
databases:
    - homestead
```

### 自定义变量

可以自定义一些虚拟机上使用的自定义变量

```yaml
variables:
    - key: APP_ENV
      value: local
```

## 启动 Vagrant

进入到 Homestead 的文件夹中

```sh
vagrant up
```

第一次启动时，Vagrant 执行

- 以导入的 Homestead 虚拟机盒子为模版，新建一台虚拟机
- 按照 `Homestead.yaml` 的配置信息，对新虚拟机进行配置
- 启动虚拟机

远程连接到虚拟机

```sh
vagrant ssh
```

关闭虚拟机

```sh
vagrant halt
```

