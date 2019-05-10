# 使用 git hooks

## 背景

当我们在自己的服务器上安装了一个 Git 服务，并创建了一个 Git 的仓库用来存放我们 push 过来的代码。这种情况，就类似我们在服务器上搭建了一个属于自己的 GitHub

然而，我们 push 代码到服务器的 Git 上后，可以在服务端的 Git 文件夹发现，我们在这里根本找不到代码

> 实际上，我是想直接通过 `git push` 的方法把代码存放到服务端后再实现自动化的部署
> 但按照上面出现的情况，好像是做不到了

实际上，上面的情况是正常的，但要再实现自动化部署，需要用到 git hooks

> 具体思路是：hook 住代码 push 完成后的阶段，调用自动部署的脚本

## Git hooks

当使用 `git --bare init xxx.git` 在服务端创建了代码仓库后，可以看到文件夹中包含了 hooks 的文件夹，这里就是存放 git hooks 的地方

可以看到这些 hook 文件都包含了 `.sample` 后缀，顾名思义，这些是样板，并不会执行。要想 Git 执行这些 hooks, 那么 hook 文件需要无后缀名

当 hooks 为 pre-xxx 阶段时，当脚本文件的执行结果返回 0 ，该 xxx 操作才会执行, 如 `commit`

---

在执行 git hooks 时，会根据 Shebang 来选择执行 hooks 的代码解释器，即文件的第一行代码

- 使用 Bash: `#!/usr/bin/bash`
- 使用 JavaScript: `#!/path/to/node`
- 使用 Python: `#!/path/to/python`
- 使用 Ruby: `#!/path/to/ruby`

### Git hooks 种类

#### 客户端 hooks

目前有兴趣了解的一些 hooks

- `pre-commit`
    - 运行测试
    - 检查代码风格
    - 使用 `git commit --no-verify` 可以绕过 hook
- `prepare-commit-msg`
    - 顾名思义：准备提交信息。实际上，准备的是填写提交信息的模版
- `commit-msg`
    - 接受「提交信息」的临时保存的文件位置作为参数
    - 可以用于检查提交信息的格式
    - 当脚本返回非 0 值时，`commit` 操作中断
- `post-commit`
    - 提交操作完成后执行，不接受参数
    - 一般用于通知

#### 服务端 hooks

目前有兴趣了解的一些 hooks

- `pre-receive`
    - 客户端发起 push 操作时最新被调用的 hook
    - 返回非 0 值时，拒绝客户端的 push 操作
    - 可以用来控制文件写权限
- `update` 
    - 与 `pre-receive` 相近，但具体到每个分支。当用户向多个分支 push 时，`pre-receive` 只会执行一次，而 `update` 会为这多个分支都执行一次
    - 接受参数：分支名称，push 前引用所指向内容的 SHA-1 值，新增修改内容的 SHA-1 值
    - 当脚本返回非 0 值时，只用对应的分支的 push 操作被拒绝，其他分支正常
- `post-receive`
    - 在 push 完成后执行，用于通知，持续集成的工作
    - 在此脚本完成执行前，Git 服务端始终与客户端保持链接


## References

- [自定义 Git - Git 钩子](https://git-scm.com/book/zh/v2/自定义-Git-Git-钩子#r_email_hooks)



