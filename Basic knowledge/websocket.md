# WebSocket 基本概念

## WebSocket 的好处

1. 客户端与服务端只建立一个 TCP 连接，客户端与服务端通信期间复用一个连接
2. 使用 WebSocket, 服务端可以主动向客户端推送消息，而传统的 HTTP 请求，只能由客户端发起对服务端的请求
3. WebSocket 的协议头部信息占用空间比 HTTP 更小，减少了数据的传输量

> 使用 WebSocket, 服务端可以主动向客户端进行消息推送这一特点，对于传统的做法，都是使用轮询的做法，即不断（每隔特定一段时间）由客户端向服务端进行请求，达到了功能上看似是服务端对客户端进行消息推送
> 而使用 WebSocket, 能真正做到服务端向客户端进行消息推送

## WebSocket 工作方式

与 HTTP 类似，WebSocket 协议是建立在传输层协议 TCP 之上，因此可以看作成：WebSocket 与 HTTP 协议一样，是应用层上的协议

### WebSocket 握手

实际上，在建立起 WebSocket 连接之前，服务端和客户端是使用 HTTP 协议进行沟通，而这个沟通过程的目的，是为了建立起 WebSocket 连接

因此，建立起 WebSocket 之前，会首先通过 TCP 协议层的 **三次握手**，然后在通过 HTTP 协议方式，进行 **「一次握手」**，最后建立起 WebSocket 连接

#### 客户端 HTTP 请求报文

而 WebSocket 协议，可以看作是 HTTP 协议的升级版，在「一次握手」时，客户端发起的 HTTP **请求报文** 的头部信息会与平常的请求有所不同，而其中最具有特征的是

```
// 请求服务器将协议升级为 WebSocket
upgrade: websocket
Connection: Upgrade
```

其他有特征的字段

```
// 客户端用于安全校验的 Key
Sec-WebSocket-Key: xxx(base64)

// 子协议号，服务端可以根据此字段对 WebSocket 连接作特定处理，由业务决定
Sec-WebSocket-Protocol: chat, superchat

// 版本号
Sec-WebSocket-Version: 13
```

#### 服务端 HTTP 响应报文

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: xxxx
Sec-WebSocket-Protocol: chat
```

服务端响应的 HTTP 报文，告诉客户端：服务端正在切换协议

客户端将会检验 `Sec-WebSocket-Accept` 的值

而客户端也可以使用 `Sec-WebSocket-Protocol` 中的子协议来进行处理

---

经过上面的 HTTP 协议层的握手，WebSocket 协议的连接就建立成功，此后，服务器与客户端可以进行双工通信，同时收发信息

### WebSocket 发送消息

当建立起 WebSocket 连接后，服务端与客户端就可以互相发送消息

一般而言，服务端发送消息，使用 `send` 方法（不同语言实现不同，此处为关键字），则客户端的 `message` 事件监听会被触发；客户端发送消息，使用 `send` 方法，则服务端的 `message` 事件监听会被触发

数据帧的标识

- `fin` 通过 WebSocket 发送的消息，可能会因为数据量过大而进行切片，而 `fin` 则表示此帧是否为最后一帧
- `rsv1`, `rsv2`, `rsv3` 用于扩展，不细说
- `opcode` 用于表明数据帧的内容类型
    - 附加数据帧 0
    - 文本数据帧 1
    - 二进制数据帧 2
    - 连接关闭数据帧 8
    - ping 数据帧 9
    - pong 数据帧 10
- `masked` 是否进行掩码处理
    - 客户端发送给服务端 1
    - 服务端发送给客户端 0
- `payload length` 标识数据长度
- `masking key` 当 `masked` 为 1 时存在，用于解密数据
- `payload data` 数据

> 对于 `opcode` 中的 `ping`, `pong`
> 用于心跳检测，检测对方是否还在响应这个当前连接，当 A 收到 `ping` 数据帧后，A 必须返回一个 `pong` 数据包作为响应
> 
> `masked` 处于安全考虑，客户端向服务端发送数据前，需要对数据帧进行掩码运算加密处理，当服务器收到无掩码帧，则会关闭连接；若客户端收到了掩码帧，也会将连接关闭
> 因此，客户端发出的数据帧 `masked` 为 1，服务端发出的数据帧 `masked` 为 0




