# head 中的标签

## title

文档标题

## base

为页面上的相对 URL 提供一个基地址，但很少使用，一般直接在 JS 中控制

## meta

- 使用方法是填写键值对，可以有多个
- 属性中，`name` 为键，元信息名称；`content` 为值，元信息值
- 可以使用自定义的 `name` 和 `content`, 其中，`name` 和 `content` 的格式，需要写入和读取双方协定好（双方一般也就是浏览器厂商和开发者）

```html
<meta name="meta-name" content="meta-content">
```

### 具有 charset 的 meta

定义文档编码

```html
<meta charset="UTF-8">
```

### 具有 http-equip 的 meta

表示执行某个命令

```html
<meta http-equiv=“content-type” content=“text/html; charset=UTF-8”>
```

- 表示设置 http 头部的 content-type, 并设置了编码

http-equip 的命令还有

- `x-ua-compatible` 告知浏览器使用的渲染兼容模式
- `content-security-policy`
    - 用于设置内容安全策略
    - 设置允许访问的服务端地址和脚本地址，避免 [XSS 攻击](../../Basic%20knowledge/cross-site-attack.md)

### viewport 的 meta

```html
<meta name=“viewport” content=“width=500, initial-scale=1”>
```

- 对于移动端的页面，一般还会使用 `user-scalable=no`


