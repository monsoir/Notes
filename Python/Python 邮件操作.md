# Python 邮件操作

- [发送邮件](#发送邮件)
    - [创建访问对象](#创建访问对象)
    - [向服务器打招呼](#向服务器打招呼)
    - [开始 TSL 加密](#开始-tsl-加密)
    - [登录到 SMTP 服务器](#登录到-smtp-服务器)
    - [发送电子邮件](#发送电子邮件)
    - [断开与 SMTP 服务器的连接](#断开与-smtp-服务器的连接)
- [收取邮件](#收取邮件)
    - [创建访问对象](#创建访问对象)
    - [登录到 IMAP 服务器](#登录到-imap-服务器)
    - [搜索电子邮件](#搜索电子邮件)
        - [选择文件夹](#选择文件夹)
        - [进行搜索](#进行搜索)
    - [大小限制](#大小限制)
    - [取邮件](#取邮件)
    - [使用 pyzmail 来解析邮件](#使用-pyzmail-来解析邮件)
        - [创建一个 pyzmail 对象](#创建一个-pyzmail-对象)
        - [获取邮件的信息](#获取邮件的信息)
        - [获取邮件的正文内容](#获取邮件的正文内容)
    - [删除邮件](#删除邮件)
        - [普通删除，放入垃圾箱的](#普通删除放入垃圾箱的)
        - [永久删除](#永久删除)
- [断开与 IMAP 服务器的连接](#断开与-imap-服务器的连接)

## 发送邮件

使用内置的 smtplib 模块

### 创建访问对象

需要提供的信息

- 邮件提供商的 SMTP 服务器地址
- 端口

---

```py
import smtplib

smtpObj = smtplib.SMTP(theSMTPServerDomainName, port)
```

SMTP 对象包含了连接信息，以及发送邮件的方法

一般来说，SMTP 支持命令加密标准 TLS, 这个标准使用的端口几乎总是 587

若 `smtplib.SMTP()` 方法调用不成功，则表示 SMTP 服务器不支持 TLS 端口 587，此时可以使用 SSL

```py
smtpObj = smtplib.SMTP_SSL(theSMTPServerDomainName, port)
```

而这个端口，一般为 465

> 若没有连网，则将会抛出异常，gaetaddrinfofailed 之类的异常


### 向服务器打招呼

建立了 SMTP 对象之后，需要确保与服务器的连接正常，必须要调用此方法，否则之后的方法调用将会出错

```py
ehloResult = smtpObj.ehlo()
```

返回的 ehloResult 是一个元组，若第一项为 `250`, 则表示问候成功

> 250 在 SMTP 中表示成功

### 开始 TSL 加密

这一步使得连接处于加密状态中

```py
tlsResult = smtpObj.starttls()
```

返回的 tlsResult 是一个元组，若第一项为 `220`, 则表示服务器已经准备好了

> 这一步是使用 TLS 标准加密需要做，使用 SSL(465 端口) 的连接不需要进行这一步，因为加密早已做好了

### 登录到 SMTP 服务器

```py
loginResult = smtpObj.login(yourEmailAddress, password)
```

返回的 loginResult 是一个元组，若第一项为 `235`, 则表示认证成功，若密码错误，则会抛出异常 `smtplib.SMTPAuthenticationError`

> 账号密码不应该写在程序中，而应该使用 `input()` 来获取用户的输入

### 发送电子邮件

发送邮件需要 3 个参数：

- 发件人的电子邮件地址字符串，fromWhom
- 收件人的电子邮件地址字符串 或 多个收件人地址字符串组成的 list, toWhom
- 电子邮件正文字符串 content
    - 必须以 `Subject: xxxx\n` 开头，作为主题行
    - `\n` 后为正文内容

```py
sendResult = smtpObj.sendmail(fromWhom, toWhom, content)
```

返回的结果为一个 dict, 包含的是传送失败的每个收件人

所以，空的 dict 意味着发送成功了

> ⚠️ Gmail 会需要程序专用密码，设置 Gmail 的应用程序专用密码

### 断开与 SMTP 服务器的连接

```py
quitResult = smtpObj.quit()
```

返回的 quitResult 是一个原则，若第一项是 `221`, 则表示会话结束了

---

## 收取邮件

收取邮件，Python 有内置的 imaplib 模块，但第三方 imapclient 模块更易用

由于 imapclient 下载后的电子邮件格式比较复杂，要将格式转换成简单的字符串，使用第三方 pyzmail 模块

- [👉 imapclent 文档](https://imapclient.readthedocs.io/en/stable/)

### 创建访问对象

需要提供的信息：

- 邮件服务提供商的 IMAP 服务器域名

```py
import imapclient

imapObj = imapclient.IMAPClient(theIMAPServerDomainName, ssl=True)
```

由于大多数邮件提供商要求 SSL 加密

### 登录到 IMAP 服务器

```py
imapObj.login(emailAddress, password)
# 将返回一个服务器的响应
```

### 搜索电子邮件

搜索电子邮件需要分为 2 步

1. 选择要搜索的文件夹
2. 调用搜索方法

#### 选择文件夹

获取文件夹列表

```py
listResult = imapObj = list_folders()
```

返回的 listResult 是一个包含多个 tuple 的列表，tuple 的值根据不同的邮件服务提供商而定

---

选择一个文件夹

```py
selectResult = imapObj.select_folder(folerName, readonly=True)
```

返回的 selectResult 是一个 dict, 包含了 SELECT 的响应，基本上是可以忽略这个返回值

readOnly 是为了防止之后的操作误对邮件进行的修改，删除操作。同时，设置 `readOnly = True` 后，也不会影响邮件的已读状态

> 如果需要更改 readOnly 的值，则需要重新选择文件夹，即在此调用 `select_folder` 方法

#### 进行搜索

搜索需要使用到搜索键，关于搜索键的种类，看 「Python 编程快速上手——让繁琐工作自动化」的 P310

```py
searchResult = imapObj.search([searchKey1, searchKey2, ...])
```

返回的 searchResult 为邮件的唯一整数 ID 的列表

---

获取邮件内容，可以根据邮件的唯一整数 ID, 调用 `fetch()` 方法获取邮件内容

### 大小限制

当搜索匹配大量的邮件，Python 可能会抛出异常

```
imaplib.error: got more than XXXXX bytes
```

这是为了防止 Python 消耗过多的内存，此时应该重新连接 IMAP 服务器

可以对默认大小进行更改

```py
import imaplib # 注意是 imaplib
imaplib._MAXLINE = xxxxx
```

### 取邮件

```py
rawMessages = imapObj.fetch(UIDs, ['BODY[]'])
```

参数：

- UIDs 为 ID 列表，即可以同时获取多封邮件的内容
- ['BODY[]'] 让 fetch 下载邮件中的所有正文内容

返回的 rawMessages 是一个字典，以 UID 为键，而每一个键对应的值也是一个字典，包含了 `BODY[]` 和 `SEQ` 两个键

- `BODY[]` 邮件的实际正文
- `SEQ` 序列号，作用与 UID 类似

> 由于 `BODY[]` 对应的值内容是 RFC822 格式，专为 IMAP 服务器设计的，不易理解。因此将使用 pyzmail 模块来进行解析

### 使用 pyzmail 来解析邮件

使用 pyzmail 模块，可以轻松访问邮件的主题，正文，收件人，发件人等字段

#### 创建一个 pyzmail 对象

```py
import pyzmail

message = pyzmail.PyzMessage.factory(rawMessages[mailUID]['BODY[]'])
```

调用 pyzmail.PyzMessage.factory 方法，传入原始邮件的 `BODY[]` 部分，返回的结果为 PyzMessage 对象

#### 获取邮件的信息

```py
# 获取邮件主题
message.get_subject()

# 获取发件人
message.get_addresses('from')

# 获取收件人
message.get_addresses('to')

# 获取抄送人
message.get_addresses('cc')

# 获取密件抄送人
message.get_addresses('bcc')
```

> `get_addresses` 方法返回的是包含 tuple 的列表，其中 tuple 的结果为 (邮箱名, 邮箱地址)

#### 获取邮件的正文内容

电子邮件的内容可以是

- 纯文本
- HTML
- 纯文本 和 HTML 的混合

---

识别电子邮件内容

- 若仅仅是纯文本，则将 PyzMessage 对象的 `html_part` 置为 `None`
- 若仅仅是 HTML, 则将 PyzMessage 对象的 `text_part` 置为 `None`
- 若是混合物，则 `html_part` 与 `text_part` 都不为 `None`

---

获取电子邮件内容

- 获取纯文本部分

    ```py
    if (message.text_part):
        result = message.text_part.get_payload()
    ```

- 获取 HTML 部分

    ```py
    if (message.html_part):
        result = message.html_part.get_payload()
    ```
    
由于 `get_payload()` 方法返回的是 bytes 类型数据，需要调用 `decode(编码)` 来进行解码

```py
message.html_part.get_payload(message.html_part.charset)
```

> 邮件内容的字符编码，保存在了 message.text_part.charset 或 message.html_part.charset 中

## 删除邮件

在删除邮件前，选择邮箱时，需要将 `readOnly` 参数设置为 `False`

### 普通删除，放入垃圾箱的

```py
deleteResult = imapObj.delete_messages(UIDs)
```

传入需要删除邮件的 UID 列表

此方法将为邮件添加上 `\Delete` 标记

返回的 deleteResult 为一个 dict, 每个键值对是一个邮件 ID 及其消息标记

### 永久删除

```py
deleteResult = imapObj.expunge()
```

永久删除带 `\Delete` 标记的邮件

返回的 deleteResult 是一条消息

## 断开与 IMAP 服务器的连接

```py
imapObj.logout()
```

> 若程序运行的时间过长，IMAP 服务器会自动断开断开，此时调用 imapObj 的方法将会抛出异常。此时需要重现调用 `imapclient.IMAPClient()` 方法



