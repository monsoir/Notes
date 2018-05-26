# 使用 Node 创建 WebSocket

在 Node 平台上搭建 WebSocket 服务，使用的比较多的是 [ws 模块](https://github.com/websockets/ws)

ws 模块包含了 WebSocket 的服务端与客户端的创建，但客户端不支持浏览器的使用

下面使用的是 ws + TypeScript 的组合来开发一个 Demo, Demo 包含了服务端与客户端（命令行）

> 使用 TypeScript 开发，是因为 TypeScript 由 API 描述的“头文件”，更加容易理解 ws 模块的使用方法

## 服务端

过程描述

- 服务开启后，触发 `listening` 事件，打印出 `web socket begins listening`
- 服务端监听到了客户端连接 `connection` 事件后，为此 socket 注册一系列事件后，打印出客户端的 IP 地址，并向客户端发送 `hi` 消息
- 当收到了客户端发来的消息，`message` 事件后，将打印出消息内容，同时检测消息内容是否为 `terminal` 字符串，若是，则关闭与此客户端的连接，并在 3 秒后关闭服务端的 WebSocket 服务
- 当监听到客户端断开连接，`close` 事件后，将打印出关闭原因的代码及关闭的原因描述
- 当监听到客户端连接错误后，将打印出错误信息 

```typescript
import * as ws from 'ws';

// 创建一个 WebSocket 服务器，监听的是 30002 端口
const webSocketServer = new ws.Server({
  port: 30002,
});

// 监听的是 WebSocket 服务开始监听的事件
webSocketServer.on('listening', (socket: ws) => {
  console.log('web socket begins listening');
});

// 监听的是 WebSocket 服务被客户端连接上的事件
webSocketServer.on('connection', (socket: ws, req) => {

  // 监听的是 服务端收到了客户端发来的消息 事件
  socket.on('message', (data) => {
    console.log(data);
    if (data === 'terminate') {
      socket.close();
      setTimeout(() => {
        webSocketServer.close();
      }, 3000);
    }
  });

  // 监听的是，服务端收到了客户端关闭连接的事件，由客户端发起的关闭
  socket.on('close', (code, reason) => {
    console.log(code);
    console.log(reason);
  });

  // 监听的是，WebSocket 通信过程中出错的事件
  socket.on('error', (error: Error) => {
    console.log('error:');
    console.log(error);
  });

  const ip = req.connection.remoteAddress;
  console.log(ip + ' is connected');
  socket.send('hi');
});
```

在 `connection` 事件监听中

- `socket` 参数 来自客户端的 socket 对象
- `req` 来自客户端发来的 HTTP 请求，建立 WebSocket 连接时，通过 HTTP 协议来完成

## 客户端

过程描述

- 当监听到客户端 WebSocket 连接打开后，`open` 事件，将打印出 `web socket opened`
- 当收到来自服务端的消息，`message` 事件后，将打印出消息内容，并在 5 秒后，向服务端发送文本内容为 `terminate` 的消息
- 当监听到错误发生，`error` 事件后，将打印出错误信息
- 当监听到服务端关闭连接，`close` 事件后，将打印出 `???`
- 当监听到服务端发来心跳包检测，`ping` 事件后，将打印出 ping 包内容 (Demo 中没有进行 ping, 只是意思一下)
- 当监听到服务端发来心跳包响应，`pong` 事件后，将打印出 pong 包内容 (Demo 中没有进行 pong, 只是意思一下)

```typescript
import * as ws from 'ws';

// 创建 WebSocket 客户端，连接的是本机的 30002 的 WebSocket 服务
const webSocketClient = new ws('ws://127.0.0.1:30002');

// 监听的是 客户端获知 WebSocket 连接成功后的事件
webSocketClient.on('open', () => {
  console.log('web socket opened');
});

// 监听的是 客户端收到了来自服务端的消息
webSocketClient.on('message', (data) => {
  console.log(data);

  setTimeout(() => {
    webSocketClient.send('terminate');
  }, 5000);
});

// 监听的是 通信过程中出错的事件
webSocketClient.on('error', (error: Error) => {
  console.log(error);
});

// 监听的是 WebSocket 连接被关闭的事件，由服务端发起的关闭
webSocketClient.on('close', () => {
  console.log('???');
});

// 监听的是 收到来自服务端的心跳检测 ping 的事件
webSocketClient.on('ping', (data) => {
  console.log('ping' + data);
});

// 监听的是 收到来自服务端的收到心跳检测 pong 的事件
webSocketClient.on('pong', (data) => {
  console.log('pong' + data);
});
```

## 最后

ws 模块的 Github 页面由很详细的使用方法说明

- [ws 项目地址](https://github.com/websockets/ws)
- [ws API 描述](https://github.com/websockets/ws/blob/master/doc/ws.md)
- [Demo 代码地址](https://github.com/pennyworthit/node-websocket-demo)


