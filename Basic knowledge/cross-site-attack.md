# 跨站攻击手段

## XSS - Cross Site Scripting 跨站脚本攻击

用户提交数据时，包含了 JavaScript 脚本的文本，若服务端没有进行过滤或转义其中的脚本，当其他用户请求并显示该内容时，前端会执行其中的 JavaScript 代码

## CSRF - Cross Site Request Forgery 跨站请求伪造

部分敏感的请求使用了 GET, 并通过了可跨域的标签进行了请求，如 `<script>`, `<img>`, 请求时，会自动带上了 cookies 中的 SessionId 等可以标记用户的信息，实现了从危险网站 B 窃取网站 A 的 cookies, 并进行了请求

预防：

- 敏感的请求尽可能使用 POST, DELETE, PUT 等标准的 HTTP 方法
- 在表单中添加隐藏的 token 字段，这样第三方网站无法拿到这个 token
- 使用 JWT 的 token, 需要进行敏感信息操作时，手动将 token 放到请求头中

