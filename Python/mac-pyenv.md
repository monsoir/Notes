# Mac 下 pyenv 的使用

## 安装

```sh
brew install pyenv
```

## 配置

在 `~/.bash_profile` 中设置

```
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

否则会出现一下的错误

```
...
must supply either home or prefix/exec-prefix -- not both
...
```

## 使用

查看已安装的 Python 版本

```
pyenv versions
```

结果中前面带 `*` 的为当前正在使用的版本，如

```
  system
* 2.7.14 (set by /Users/xxx/Developer/workflows/.python-version)
```

---

查看可安装的版本

```
pyenv install --list
```

---

设置版本

可以为 3 种环境设置 Python 的版本

- Shell 当前的 shell 环境
- local 当前的文件夹，会在当前文件夹中生成一个隐藏文件 `.python-version`
- global 全局环境

```
pyenv global|local|shell 2.7.14
```


