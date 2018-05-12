# 关于 JWT 的安全问题

## 安全问题的由来

由于 JWT token 本身包含了信息，以及信息校验，因此，token 是可以脱离服务端存在，只要 token 的过期时间没到，它都是有效的

对来说应用来说，如浏览器，我们一般会将 token 存放到 LocalStorage 中，方便下次需要时可以获取到。

但这里有个问题，就是浏览器中可能会存在 XSS 攻击（跨站脚本攻击 Cross Site Scripting），即页面中被嵌入了恶意的 JavaScript 代码，如窃取 token, 那么浏览器会执行这段代码：获取 token, 并发送到另外某个服务器上

在被窃取的 token 的有效期内，攻击者都可以使用这个 token 进行登录，冒充身份。由于服务器对 JWT token 的校验存在局限（ JWT token 所携带的信息是否与加密字段相符，过期时间），服务器对于有效的 token 的请求都是有问必答

## 防止 JWT 被窃取的方法

- 适当缩短 token 的有效时间
- 限制 token 的使用次数，那就需要增添多一个域来记录 token 的使用次数

实际上，一般会将 token 存放在类似 Redis 之类的缓存中，因此可以在 Redis 中对 token 再设定一个过期时间

