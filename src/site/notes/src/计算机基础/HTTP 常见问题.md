---
{"dg-publish":true,"dg-permalink":"HTTP 常见问题","permalink":"/HTTP 常见问题/"}
---


#计算机网络 #HTTP

## HTTP 常见状态码？

- 1XX
	- 提示信息，协议处理的**中间状态**
	- 101 （协议切换）
- 2XX
	- **成功**，报文被正确处理
	- 200、204、206
- 3XX
	- **重定向**，资源位置发生变动
	- 301、302、304
- 4XX
	- **客户端**错误，请求报文有误
	- 400、403、404
- 5XX
	- **服务端**错误，在处理时发生了内部错误
	- 500、501、502、503

## GET 和 POST 的区别？

1. GET：
	- 从服务器**获取**资源
	- 操作是幂等的
	- **不安全**：参数直接放到 URL 上
	- 提交数据大小有限制（Chrome 浏览器大概在 8182 字符左右）
2. POST:
	- 向服务器**提交**资源
	- 操作不是幂等的

## HTTP 和 HTTPS 的区别？

1. 加密：HTTP 是**明文传输**，不通过加密的，安全性无法保证
2. 端口：HTTP 是 80，HTTPS 是 443
3. 证书：需要申请证书
4. 响应速度：由于 HTTP 少了 **SSL 握手**的过程，会比 HTTPS 要快

## HTTP 是不保存状态的协议， 如何保存用户状态？

由于服务端不会对客户端进行标记，所以说 HTTP 是无状态协议。

几种方式：
1. **Session**：将 SessionId 下发到客户端，并建立映射；
	- 安全
	- 分布式系统下，需要**共享  Session**，如 Redis 实现。
2. **纯 Cookie**：将状态信息存储在客户端的 Cookie 当中；
	- 不安全
	- 存储的成本较高
3. **JWT**：将用户信息加密后生成 Token 下发到客户端
	- 不需要使用数据库来共享，任意一个分布式结点都能解密；
	- 客户端依旧需要存储

如果 Cookie 被禁用了，可以：
- LocalStorage
- 放到请求的查询字符串中

## 与缓存相关的 HTTP 头部有哪些?

- *Cache-Control*：控制缓存的规则
	- max-age：失效时间
	- no-cache：禁止缓存
	- ...
- *Expires*：设置缓存的**过期时间**，会覆盖掉 `Cache-Control.max-age`
- *Last-Modified*：资源最后**修改时间**
- *Etag*：资源的**唯一标识**

## HTTPS 的加密过程？

使用两种加密方式：
- 非对称加密：客户端（浏览器）生成密钥的过程
- 对称加密：内容传输的过程

![计算机网络-HTTPS 2.png](/img/user/attachments/images/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C-HTTPS%202.png)

## 使用 HTTP 长连接有哪些优点?

1. 定义
	- HTTP 长连接和短连接，实质上是 **TCP 协议**的长连接和短连接
	- 长连接是指在一个连接上，可以连续发送多个数据包，会在请求头加上 *Connection: keep-alive*
	- 在 HTTP 1.1+ 中，默认使用长连接
2. 优点
	- 减少**三次握手**的次数
	- 减少 TCP[[src/计算机基础/拥塞机制\|慢启动]]的影响
3. 缺点
	- 对 Server 的**负载**会增加
4. 适用场景
	- 操作频繁，点对点的通讯
	- 连接数不能太多

## 简述 HTTP 1.0 / 1.1 的区别

[HTTP 1.0 与 1.1 的区别](HTTP%201.0%20与%201.1%20的区别.md)

## HTTP 3 的特点？

1. 使用 UDP （即 QUIC，*Quick UDP Internet Connections*）作为传输协议
2. 连接建立快
3. 使用 QPACK 来压缩
4. 解决了队头阻塞的问题

QUIC 为啥快？

- TCP 的三次握手
- 拥塞机制的慢启动
- 丢包导致的队头阻塞

## 什么是 Chunked 机制？

Chunked 机制，即**分块传输**编码（Chunked Transfer Encoding），是 **HTTP/1.1**中的传输协议，允许服务器将数据分成**多个块**（chunk）逐个发送给客户端。

特点：
1. 无需预先知道内容的**长度**：适合动态生成内容或流式传输
2. 保持**持久**连接
3. 支持**动态**头部

协议示例：

```shell
HTTP/1.1 200 OK              # 状态码，无法修改
Content-Type: text/plain
Transfer-Encoding: chunked   # 表示消息体采用分块传输

25                                    # 块大小（16进制）+ CRLF 回车
This is the data in the first chunk   # 块数据 + CRLF
1C
and this is the second one
3
con
8
sequence
0
```


## 参考资料

> https://mp.weixin.qq.com/s/amOya0M00LwpL5kCS96Y6w