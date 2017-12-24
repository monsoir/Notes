# macOS 下 React (Native) 项目中植入 airbnb 代码规范

- [安装eslint](#安装eslint)
- [安装依赖](#安装依赖)
- [创建.eslintrc.js](#创建eslintrcjs)
- [Reference](#reference)

## 安装eslint

```shell
npm install -D eslint
```

## 安装依赖

直接在 bash 中运行

```shell
(
  export PKG=eslint-config-airbnb;
  npm info "$PKG@latest" peerDependencies --json | command sed 's/[\{\},]//g ; s/: /@/g' | xargs npm install --save-dev "$PKG@latest"
)
```

export 只在 bash 的一次 session 中有效

上述脚本会产生并自动运行等同下面的命令

```shell
npm install --save-dev eslint-config-airbnb eslint@^#.#.# eslint-plugin-jsx-a11y@^#.#.# eslint-plugin-import@^#.#.# eslint-plugin-react@^#.#.#
```

## 创建.eslintrc.js

```shell
./node_modules/.bin/eslint --init
```

之后，按照命令行中的指示一直走下去，建立起配置文件，
最后，.eslintrc.js 中出现了配置代码，其中，`extends` 指向的就是 airbnb

## Reference

- eslint
	- [https://www.npmjs.com/package/eslint](https://www.npmjs.com/package/eslint)

- eslint-config-airbnb
	- [https://www.npmjs.com/package/eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)

- eslint 配置文档
	- [http://eslint.org/docs/user-guide/configuring#extending-configuration-files](http://eslint.org/docs/user-guide/configuring#extending-configuration-files)

- Airbnb JavaScript 代码规范
	- [https://github.com/sivan/javascript-style-guide/blob/master/es5/README.md](https://github.com/sivan/javascript-style-guide/blob/master/es5/README.md)

