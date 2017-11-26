# 范围请求的使用

需求：在线传输视频，并且可以按需加载，即不一次性请求所有视频数据，并且，可以实现自由的快进和后退

## 一般做法

在前端页面中播放视频，可以使用 `<mp4>` 标签，并将 `src` 的值设置为视频的 URL, 但这种做法在一般情况可行，但是最少有下面一个缺点：

**不能在进度条上实现快进或后退，一旦用户移动了进度条，整个视频会回到原点重新加载**

因此，需要的是，按需加载，给服务器发送一个范围值，然后服务器发送该段数据给客户端，而这一个需求的实现，需要使用到 HTTP 协议中头部的 `Content-Range` 字段

## 实现

实现数据的切片传输，我们需要用到的 HTTP 头部字段有

- HTTP 状态码 206
- Range 字段
- Accept-Ranges 字段
- Content-Range 字段
- Content-Length 字段
- Content-Type 字段

### HTTP 状态码 206

需要知道的

- 206 Partial Content
- 请求成功，并且消息体中包含了所请求的数据区间，该区间在请求头部中的 `Range` 中指定

额外的

- 包含多个数据区间，响应的 `Content-Type` 为 `multipart/byteranges`

### Range 字段

需要知道的

- 属于请求首部的字段，告知服务器返回文件的哪一个部分
- 若服务器返回了的内容是响应范围，则需要使用状态码 206
- 若请求范围不合法，则服务器要返回状态码 416 Range Not Satisfiable, 表示客户端请求出错
- 当然，服务器也可以忽略 `Range` 字段，从而返回整个文件，并使用状态码 200

Range 值的语法

```
Range: <unit>=<range-start>-
Range: <unit>=<range-start>-<range-end>
Range: <unit>=<range-start>-<range-end>, <range-start>-<range-end>
Range: <unit>=<range-start>-<range-end>, <range-start>-<range-end>, <range-start>-<range-end>
```

> unit 范围值所采用的单位，一般为 bytes
> range-start 整数，范围的起始值
> range-end 整数，范围的结束值，也是一个可选值，如果不存在，则表示一直到文件的结束

额外的

- 一个 `Range` 中，可以一次性请求多个部分，服务器要以 `multipart` 文件形式将内容返回，参照上文

### Accept-Ranges 字段

- 服务器使用的响应头部字段
- 如果服务器的响应中包含这个字段，以为这服务器支持范围请求
- 这个字段的值，用来定义范围请求时，范围所采用的单位，一般为 bytes, none 值表示不支持范围请求

### Content-Range 字段

- 服务器使用的响应头部字段
- 表示响应体中包含的数据，在整个文件中的位置

Content-Ranges 语法

```
Content-Range: <unit> <range-start>-<range-end>/<size>
Content-Range: <unit> <range-start>-<range-end>/*
Content-Range: <unit> */<size>
```

> unit 范围值所采用的单位，一般为 bytes
> range-start 范围的起始值
> range-end 范围的结束值
> size 整个文件的大小，若文件大小未知，则用`*`表示

### Content-Length 字段

- 服务器使用的响应头部字段
- 表示响应体的大小，一个十进制的数字

### Content-Type 字段

需要知道的

- 客户端和服务端都会使用到的响应头部字段
- 表示资源的 MIME 类型
- 在响应头部中时，用来告诉客户端，返回的内容实际的类型
- 在请求头部中时，用来告诉服务端，实际发送的内容的类型

额外的

- 浏览器可能会对资源进行嗅探，若需要阻止浏览器对资源的嗅探，可以将 `X-Content-Type-Options` 设置为 `nosniff`, 但不一定有效

Content-Type 语法

```
Content-Type: text/html; charset=utf-8
Content-Type: multipart/form-data; boundary=something

Content-Type: <media-type>; [charset=<charset> | boundary=<boundary>]
```

> media-type 资源类型
> charset 字符编码
> boundary 若消息体中存在多个部分，则 boundary 时必须的，用于分隔这多个部分的内容

**通过上面描述的 HTTP 头部字段，可以知道，范围请求的实现侧重于服务端的实现**

## Node.js 实现的服务端支持范围请求

下面的代码是在 Express 框架中实现的，代码来自 References 中 「Node.js发送视频流」代码

```js
  const stat = fs.statSync(filePath); // 获取文件信息
  let fileSize = stat.size;
  let range = req.headers.range;
  if (range) {
    //有range头才使用206状态码
    
    // 使用正则表达式截取范围
    let parts = range.replace(/bytes=/, "").split("-");
    let start = parseInt(parts[0], 10);
    let end = parts[1] ? parseInt(parts[1], 10) : start + 999999;

    // end 在最后取值为 fileSize - 1 
    end = end > fileSize - 1 ? fileSize - 1 : end;

    let chunksize = (end - start) + 1;
    let file = fs.createReadStream(filePath, { start, end });
    
    // 范围请求状态码为 206
    res.statusCode = 206;
    res.set({
      'Content-Range': `bytes ${start}-${end}/${fileSize}`,
      'Accept-Ranges': 'bytes',
      'Content-Length': chunksize,
      'Content-Type': 'video/mp4',
    });
    file.pipe(res);
  } else {
      let head = {
          'Content-Length': fileSize,
          'Content-Type': 'video/mp4',
      };
      res.statusCode = 200;
      res.set({
        'Content-Length': fileSize,
        'Content-Type': 'video/mp4',
      });
      fs.createReadStream(filePath).pipe(res);
  }
```

## References

- [206 Partial Content](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/206)
- [Range](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Range)
- [Accept-Ranges](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Ranges)
- [Content-Range](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Range)
- [Content-Length](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Length)
- [Content-Type](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type)
- [MIME 类型](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types)
- [Node.js发送视频流](http://blog.csdn.net/liuyaqi1993/article/details/76560401)


