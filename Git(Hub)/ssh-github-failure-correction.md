# 解决 ssh 登陆 Github 失败问题

当使用 `git clone git@github.com......git` 时，发生错误

错误依次为

- Permission denied (publickey). Please make sure you have the correct access rights
- The agent has no identities.
- ~/.ssh/id_rsa: No such file or directory

## 错误解释

### 第一个错误

没有公私钥，那就生成

可以通过以下命令，来测试 ssh 登陆 github 的状态

```sh
ssh -vT git@github.com
```

- `-v` 打印 debug 信息
- `-T` 禁用伪终端分配，即没有 ssh 的交互界面，只显示连接状态

### 第二个错误

在 `~/.ssh/` 下缺少文件 `id_rsa` 和 `id_dsa`

The agent has no identities.

### 第三个错误

同样是因为缺少第二个错误中的文件

## 解决

1. 生成公私钥

    ```sh
    ssh-keygen -t rsa
    ```
    
    生成的文件为 `id_rsa` 和 `id_rsa.pub`, 位于 `~/.ssh/` 下

1. 后台开启 ssh-agent

    ```sh
    eval "$(ssh-agent -s)"
    ```

1. 添加私钥到 ssh-agent

    ```sh
    ssh-add ~/.ssh/id_rsa
    ```

1. 生成公钥指纹

    对于 OpenSSH 版本大于等于 6.8 的使用以下命令 `ssh -V` 可查看版本
    
    ```sh
    ssh-add -l -E md5
    ```

1. 到 Github 上添加公钥

    到个人设置页的 SSH and GPG keys 中创建 SSH key, 其实就是将刚才生成的公钥复制黏贴
    
    执行以下命令可快速复制
    
    ```
    pbcopy < ~/.ssh/id_rsa.pub
    ```

1. 测试连接

    ```sh
    ssh -vT git@github.com
    ```
    
    如无意外，应该连接就成功了

## References

- [Error: Permission denied (publickey)](https://help.github.com/articles/error-permission-denied-publickey/)
- [Github permission denied: ssh add agent has no identities](https://stackoverflow.com/questions/26505980/github-permission-denied-ssh-add-agent-has-no-identities)
- [Adding a new SSH key to your GitHub account](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)


