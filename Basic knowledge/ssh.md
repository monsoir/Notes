# SSH

- SSH 使用 [非对称加密](./asym-crypt.md) 进行身份验证

## 身份验证的途径

- 自动生成公钥-私钥，简单地加密网络连接，随后使用密码认证登录
- 人工生成一对密钥（公钥和密钥），实现免密码登录
    - **公钥** 放在待访问的电脑
    - 对应的 **私钥** 自行保管（保留在本地）

## 密钥管理

类 Unix 系统中，公钥保存在 `~/.ssh/authorized_keys` 文件中，多个公钥时，每个公钥占一行

## References

- [Secure Shell](https://zh.wikipedia.org/wiki/Secure_Shell)


