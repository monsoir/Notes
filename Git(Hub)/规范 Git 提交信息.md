# 规范 Git 提交信息

规范 Git 提交信息的功能是 Git 内置的功能，我们只需要配置好

- [配置好 Git 提交信息的模版](#配置好-git-提交信息的模版)
- [激活模版](#激活模版)
- [使用模版](#使用模版)
- [修改 Vim 配置(可选)](#修改-vim-配置可选)
- [Reference](#reference)

## 配置好 Git 提交信息的模版

首先，需要提前编写好 Git 提交信息编写模版

在用户目录下创建模版文件

```shell
touch ~/.gitmessage
```

在上述的模版文件中填写好模版，如

```
What: (Descript *what* you have done)

Why: (Descript *why* you have done that)

How: (Descript *how* you have done that)

Attach: (Descript other stuff, such as the influence, 'Gotcha things')

```

## 激活模版

在全局 Git 配置 `~/.gitconfig` 中添加模版配置

```
[commit]
    template = ~/.gitmessage
```

## 使用模版

commit 的时候，我们需要直接键入

```shell
git commit # 不带 -m 的那种
```

## 修改 Vim 配置(可选)

若使用 vim 来编写 commit 信息的话，最好为 commit message 中的内容设置自动换行

修改 `~./vimrc` 配置文件，添加下面的配置

```
autocmd Filetype gitcommit setlocal spell textwidth=80
```

## Reference

[https://juejin.im/post/59110c322f301e0057e4c182](https://juejin.im/post/59110c322f301e0057e4c182)


