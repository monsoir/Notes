# Laravel 环境配置时的错误解决

运行 `vagrant up` 时

```sh
Your VM has become "inaccessible." Unfortunately, this is a critical error
with VirtualBox that Vagrant can not cleanly recover from. Please open VirtualBox
and clear out your inaccessible virtual machines or find a way to fix
them.
```

解决：

```sh
cd <Homestead box 的路径>
rm -rf .vagrant
```

