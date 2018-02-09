# JWT Authentication Demo

[Demo 地址](https://github.com/Monsoir/jwt-authentication-demo)

- 由于 TypeScript 在编写时具有类型的约束，因此，代码提示上会比 JavaScript 要好，在做这个 Demo 的时候也顺便尝试一下 TypeScript
- 这个 Demo 中，不进行视图的渲染，只提供接口，即 RESTful API

大致内容如下

- 将 Express 的纯 JavaScript 项目改造成 TypeScript 项目
- 编写验证模块
- 使用验证模块
- 生成 JWT
- 通过验证保护路由

## 将 Express 的纯 JavaScript 项目改造成 TypeScript 项目

1. 安装 TypeScript，这里采取的是非全局安装的方式

    ```sh
    npm i -D typescript
    ```

2. 安装依赖包的 `@types` 文件

    ```sh
    npm i -D @types/express @types/body-parser @types/debug @types/morgan
    ```
    
    在使用 TypeScript 时，安装第三方依赖时，需要尽量地安装对应的 `@types` 文件，提供给 tsc 进行类型检查，否则，编译可能会不通过

3. 将脚手架的 JavaScript 文件用 TypeScript 重写一次

    这里可以使用第三方提供的脚手架，不过手动操作一次，可以了解一下流程

    在根目录下新建一个 `src` 文件夹，用于存放项目的所有源代码 `*.ts`
    
    对其中的 `app.js`, `www` 文件进行重写时，可以参照微软官方的 [TypeScript Node Starter](https://github.com/Microsoft/TypeScript-Node-Starter)
    
4. 在 `src` 的根目录下，需要添加 TypeScript 的编译配置文件 `tsconfig.json`

    这个文件描述了 TypeScript 编译为 JavaScript 的一些配置，如目标 JavaScript 的标准版本，输出目录等，更多的配置项可以参考文档 [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)
    
    例如，在这个 Demo 中，配置文件如下

    ```json
    {
        "compilerOptions": {
            "module": "commonjs",
            "outDir": "../built",
            "allowJs": true,
            "target": "es6",
            "sourceMap": true,
        },
        "exclude": [
    		"node_modules"
    	]
    }
    ```
    
    > 需要注意的是，`tsconfig.json` 需要放置在 TypeScript 文件夹的根目录下，否则，编译时会报错，找不到配置文件

5. 为了方便，在 package.json 中添加一个编译的脚本命令

    ```json
    "scripts": {
        "start": "node ./built/www.js",
        "compile": "node ./node_modules/typescript/bin/tsc -p ./src",
        "run": "npm run compile && npm start"
    },   
    ```
    
    ```sh
    npm run compile # 编译 `*ts` 文件
    npm start # 运行编译后的 `*.js` 文件，也就是启动服务
    npm run run # 编译后启动服务（想不到什么好名字了，有点啰嗦😂）
    ```

---

由于 Demo 中不进行视图的渲染，因此，我们可以对项目文件进行删减，首先是对视图文件的删除，接着是中间件使用的删减，最后的结果是

```ts
app.use(logger('dev'));
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());
app.use(express.static(path.join(__dirname, '../public')));
```

## 编写验证模块

- 编写验证模块时，我们用到了两个依赖包 `passport` 与 `passport-jwt`
- [Passport](http://www.passportjs.org) 是 Node.js 平台上进行用户验证的依赖包，支持多种策略的使用
- [passport-jwt](https://github.com/themikenicholson/passport-jwt#extracting-the-jwt-from-the-request) 是 Passport 的一种验证策略

> Passport 与验证策略的关系
> Passport 对于 Express 这种 Web 框架而言，只是一个简单的中间件；对验证这个功能来说，它是一个框架，负责调度，但不负责真正的验证工作
> 验证策略，负责真正的验证工作，如 passport-jwt, 则是使用 JWT 这种机制来进行验证，而 Passport 对此并不知情，Passport 只是负责调用相应的策略来验证
> 因此，Passport 可以调度多种验证策略

### 创建验证策略

```ts
var strategy = new Strategy(StrategyOptions, function(payload, done) {
    const user = users[payload.id] || null;
    if (user) {
      return done(null, {
        id: user.id,
        email: user.email,
      });
    } else {
      return done(new Error('User Not Found'), null);
    }
});
```

- 这里的 `Strategy` 就是 `passport-jwt`, 需要引入 `import * as passportJWT from 'passport-jwt';`
- `StrategryOptions` 是验证的配置，更多的配置选项参见 [passport-jwt](https://github.com/themikenicholson/passport-jwt#extracting-the-jwt-from-the-request), 在这里，配置项有

    ```ts
    const StrategyOptions: passportJWT.StrategyOptions = {
      secretOrKey: JWTConfig.jwtSecret, // 用于对 JWT 进行加密解密用的 secret
      jwtFromRequest: ExtractJWT.fromAuthHeaderWithScheme('jwt'), // 表示从 HTTP 报文的头部获取 JWT
      // jwtFromRequest: ExtractJWT.fromBodyField('token'), // 表示从 POST 报文的 body 中获取 JWT
    };
    ```
- 其中，获取 JWT 的地方有多种选项，都是可以配置的
- 需要注意的是，如果是从 HTTP 头部的 `Authenticate` 中获取 JWT 的话，客户端拼接头部的时候，需要将 Scheme 也加上，即 Scheme + ` ` + JWT, 如 `jwt xxx.xxx.xxx`
- 回调函数中的 `payload` 是在成功将 JWT 解码后获取到的，这个 `payload` 是我们自己赋值进去的，下面会讲到

### 注册验证策略

```ts
passport.use(strategy);
```

在这里，还有另外一种写法

```ts
passport.use('strategy name', strategy);
```

- 以上两种方法，效果大致相同，而后一种方法是指明了策略使用的名称，这个名称，将会在之后进行验证的时候用到，用来指明用哪一个已注册的验证策略
- 而不指明名称的时候，使用到策略本来自带的默认属性 `name`. 像 Demo 中用到的 passport-jwt, 它的默认名字是 `jwt`, 这可以通过查看源代码来获知 [👉 代码位置](https://github.com/themikenicholson/passport-jwt/blob/685fffea946f462627b31e839eaa7da9c6e4a37b/lib/strategy.js#L31)

### 完成验证模块

- 验证模块的工作基本上就是对验证策略的编写
- 最后，需要做的事情是
    - 初始化 Passport
    - 将 `passport.authenticate()` 作为中间件，插入到需要验证的路由中

由于在一个 App 中，我们可能会用到多种验证策略，而将这些策略写在堆放在路由或入口文件会非常难看，因此，将这些上面的步骤都写在一个文件里面，形成一个验证模块

```ts
function auth() {
  var strategy = new Strategy(StrategyOptions, function(payload, done) {
    const user = users[payload.id] || null;
    if (user) {
      return done(null, {
        id: user.id,
        email: user.email,
      });
    } else {
      return done(new Error('User Not Found'), null);
    }
  });

  passport.use(strategy);
  return {
    initialize: function() {
      return passport.initialize();
    },

    authenticate: function() {
      return passport.authenticate('jwt', JWTConfig.jwtSession);
    }
  }
}
```

- 通过将上述所讲述到的步骤，全部封装在一个 `auth` 函数中，这个函数最后返回一个对象
- 返回的对象最后会有两个方法
    - `initialize()` 初始化 Passport
    - `authenticate()` 返回一个验证的中间件
- 在调用 `passport.authenticate` 时，我们指定了第二个参数，这个参数的值是 `false`, 表明了我们不需要 Passport 生成 session, 因为在 JWT 的原理中并不需要 session

## 使用验证模块

基于上面对验证模块的封装，我们可以在路由出简单地调用验证功能

1. 创建验证对象

    ```ts
    // 引入自己写的验证模块
    import auth from './auth/auth';
    
    // 创建验证器
    const auther = auth();
    ```

2. 注册中间件

    ```ts
    app.use(auther.initialize());
    ```

3. 对路由的访问进行验证

    ```ts
    app.post('/user',  auther.authenticate(), (req, res) => {
      // 验证成功的回调
    });
    ```

---

- 事实上，在验证模块 auth.ts 中，当我们使用 `passport.use(strategy)` 时，最好是显式添加上 `name`, 为使用多种策略验证时提供便利
- 而对 auth.ts 中的 `authenticate` 方法，更是应该命名为更加表意明确的名字，如 `JWTAuthenticate`, 这样，在路由添加中间件时，可以写为

    ```ts
    app.post('/user',  auther.JWTAuthenticate(), (req, res) => {
      // 验证成功的回调
    });
    ```
    
    - 这样，我们可以清楚地看到路由验证时，我们是使用什么策略进行验证的

## 生成 JWT

关于 JWT 的详细解释，可以参考 [Introduction to JSON Web Tokens](https://jwt.io/introduction/)

简单地说，JWT 是 JSON Web Token

### 特点

- Compact 短小精干。JWT 只是一个字符串，而且这个字符串并不是很长占用空间小，传输效率高，可以放在 URL, POST 请求体，甚至是 HTTP 头部
- Self-contained 自解释。JWT 中可以包含识别用户的一些必要信息，这样，可以减少查询数据库的次数

### 使用场景

- Authentication 验证。用于减少用户的登录次数，输入密码的次数
- Information Exchange 信息交换。JWT 可以用于在不同模块中交换少量重要的信息，JWT 可以签名加密，所以还是可以的

### 结构

一个 JWT 有三个部分组成，每个部分使用 `.` 进行连接，最后成为一个字符串 `xxxx.yyyy.zzzz`

- Header `xxxx`
- Payload `yyyy`
- Signature `zzzz`

#### Header

典型地，包括

- token 的类型，一般就是 `JWT`
- Hash 算法名

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

最后，将形如上面的 JSON 转换成 Base64 字符串，构成了 Header

#### Payload

- 包含识别用户的信息
- 有一系列的预定义值，但这些值不是强制性的，只是一种规范

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

最后，将形如上面的 JSON 转换成 Base64 字符串，构成了 Payload

#### Signature

Signature 用来验证发送者是否为它所声称的用户，即验证你是不是你本人，并确保附带的信息没有经过篡改

生成一个 Signature, 需要有

- 编码后的 Header
- 编码后的 Payload
- secret
- 头部中声明的 Hash 算法

如采用的是 HMAC SHA256 算法，则 Signature 的生成方法为

```js
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

### 工作原理

- 当用户输了一次账号密码之后，服务器校验成功后，将会返回一个 JWT 到客户端，客户端将 JWT 保存在本地，而服务器并不保留用户状态的数据
- 当用户需要访问受保护的路由时，就需要手动添加上 JWT
- 由于 JWT 是自解释的，因此服务器可以从 JWT 中直接获取到识别用户的信息

> 由于使用 JWT 时，我们并没有使用到 cookies, 因此，不存在 CORS 攻击的危险

### 生成 JWT

在 Demo 中，生成 JWT, 我们

- 使用 [jsonwebtoken](https://github.com/auth0/node-jsonwebtoken). jsonwebtoken 是一个用于生成 JWT 的第三方依赖库，由于 passport-jwt 中，部分功能也是依赖着 jsonwebtoken, 于是直接干脆用这个来生成我们的 JWT
- 为了让客户端获取到 JWT, 我们需要新建一个路由 `/token`, 提供给客户端访问

    ```ts
    app.post('/token', (req, res, next) => {
        // 校验用户的账号密码输入
        // 校验成功，查询用户
        // 为用户生成 JWT
    });
    ```

---

要生成 JWT, 我们需要调用的 API 是 `jwt.sign(payload, secretOrPrivateKey, [options, callback])`, 为此，我们需要提供两个参数

- JWT 的 Payload
- 用于加密解密的 secret

在上述对 JWT 结构中，我们知道，Payload 中包含了用户的身份信息以及其他对 JWT 的设置信息（如，有效时间），而在 `sign` 方法中，我们可以将用户的身份信息，对 JWT 的设置信息分开，使得业务代码与技术代码分离，当然，代码运行的时候，它们还是会在一起的

```ts
// 用户身份信息
const tokenPayload = {
    id: user.id,
};

// JWT 的设置
const signOptions: jwter.SignOptions = {
    expiresIn: 60, // 有效时间
};

// 生成 JWT
const token = jwter.sign(tokenPayload, configs.JWTConfig.jwtSecret, signOptions);
```

在 JWTConfig 中，我们定义了 secret

```ts
const JWTConfig = {
  jwtSecret: 'secret', // 用于 encode 和 decode token
  jwtSession: {
    session: false, // 禁用 session
  },
};
```

在调用 `sign` 方法时，我们可以在最后传入一个回调函数

- 如果传入了回调函数，则异步生成 JWT, 并传入到回调函数中
- 否则，则同步生成 JWT, 函数的返回值为 JWT

## 通过验证保护路由

新建一个路由 `/user`, 模拟获取用户信息，访问这个路由是，需要客户端带上 JWT, 否则，Passport 会自动返回 `401`

```ts
app.post('/user',  auther.authenticate(), (req, res) => {
  if (req.user) {
    res.json({
      id: req.user.id,
      email: req.user.email,
    });
  } else {
    res.json({

    });
  }
});
```

- 在路由名称与路由处理函数中间，添加了 `auther.authenticate()`, 这个正是验证的中间件。路由的处理函数只会在验证成功的时候才会进行调用，否则 Passport 自动返回 `401`, 当然，验证失败的处理也是可以自定义的
- 如果将 `auther.authenticate()` 改写成 `auther.JWTAuthenticate()` 的话，会使得代码的可读性更强
- 检验成功之后，路由处理函数的 `req` 参数，会自动带上 `user` 属性，其中包含了用户信息，而这里设计到了一个问题，`req.user` 中的信息是怎么来的？？？

### 用户身份验证的数据流动

在 auth.ts 中，创建策略时

```ts
var strategy = new Strategy(StrategyOptions, function(payload, done) {
    const user = users[payload.id] || null;
    if (user) {
      return done(null, {
        id: user.id,
        email: user.email,
      });
    } else {
      return done(new Error('User Not Found'), null);
    }
});
```

- `done()` 回调函数的调用中，第二个参数，这就是路由处理函数中 `req.user` 数据的来源
- 当验证成功后，从 payload 中获取到用户信息标识
- 通过标识，定位到某个用户
- 对于这个用户，获取到需要返回的信息，包装成对象，作为 `done(null, arg2)` 的第二个参数传入
- 随后，在路由处理回调函数的 `req.user` 中可以获取到我们传入的信息

## References

- [Implementing JWT using Passport](https://blog.jscrambler.com/implementing-jwt-using-passport/)
- [passport-jwt](https://github.com/themikenicholson/passport-jwt#extracting-the-jwt-from-the-request)
- [Passport](http://www.passportjs.org)
- [jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)
- [JWT.IO](https://jwt.io)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/home.html)
- [TypeScript Node Starter](https://github.com/Microsoft/TypeScript-Node-Starter)

