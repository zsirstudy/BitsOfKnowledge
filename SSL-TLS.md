# `SSL/TLS`

`SSL`称为安全套接字，`TLS`称为传输层安全协议。

在整个网络分层中，`SSL/TLS`处于`TCP`上面，不仅支撑`HTTP`协议，还支撑`FTP,IMAP`等应用层协议。

## 加密解密

### 对称加密

客户端和服务端使用同一个密钥，客户端发送消息时，客户端使用此密钥加密，服务器用此密钥解密，反之亦然。

**存在问题：**

1. 密钥如何传输？密钥的传输也需要依赖另一个密匙，产生循环问题。
2. 密钥应如何存储？

### 双向非对称加密

客户端和服务端分别准备各自的公私钥。公私钥有两个特性：**公钥需要通过私钥计算出来，但反过来就不行**。

1. 客户端与服务器公开自己的公钥，保留自己的私钥。（客户端直到服务端的公钥，反之亦然）
2. 发消息时用自己的私钥签名，对方的公钥加密。
3. 对方收到消息时，用自己的私钥解密，再用对方的公钥验签（证明时客户端发出的）。

> **签名和验签：**私钥签名，公钥验签，目的是防止篡改。
>
> **加密和解密：**对方公钥加密，私钥解密。

**同样，存在公钥如何传输的问题**

### 单向非对称加密

​		一般网站对外完全公开，网站的提供者无法验证每个客户端的合法性，只有客户端可以验证网站的合法性。这种情况客户端并不需要公钥和私钥，只需要服务器有一对公钥和私钥。服务器将公钥给客户端，客户端给服务器发送消息时，公钥加密，服务器私钥解密。

**如何保证安全性？**

1. 从客户端到服务器端的加密通道已经打通，**两者可再次建立对称加密**
2. 客户端到服务端：对称加密密钥是xxx
3. 服务端到客户端：明文确认收到

> 这就是SSL/TLS原型，基于单向非对称加密+对称加密来实现

## 数字证书

> 问题复现：
>
> 当引入一个中间服务器，劫持服务器间的对话
>
> ![image-20220705153957643](C:\Users\86182\AppData\Roaming\Typora\typora-user-images\image-20220705153957643.png)

​		引入`CA`中间机构服务器，服务器发送公钥给CA，CA颁发一个数字证书，这个证书相当于服务器的身份证。

## `SSL/TLS`协议：四次握手

1. `TLS`客户端：明文传输，询问证书
2. 服务端：明文传输，回复证书
3. 客户端验证证书，提取公钥
4. 客户端：密文发送对称密钥
5. 服务端：明文回复

## `HTTPS`

`HTTPS = HTTP + SSL/TLS`整体的传输过程大致分为三个阶段：

1. `TCP`三次握手
2. `SSL/TLS`四次握手
3. `HTTP`对称加密传输