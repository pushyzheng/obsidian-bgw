---
{"dg-publish":true,"dg-permalink":"HTTP 1.0 与 1.1 的区别","permalink":"/HTTP 1.0 与 1.1 的区别/"}
---


#计算机网络 #HTTP

1. 缓存处理
2. 断电重传（也即节约带宽）
3. Host 请求头
4. 长连接

## HTTP 1.0 

1. 只提供**最基本的认证**
	- 不加密用户名和密码
2. 只有**短连接**
	- 意味着每次连接都要经历三次握手和四次挥手
3. 对于缓存的控制有两个头
	- If-Modified-Since
	- Expires
4. **不支持**断点重传
5. **没有 Host 头**
	- 只认为一台主机只有一个站点

## HTTP 1.1

1. 默认使用[[src/计算机基础/HTTP 常见问题#使用 HTTP 长连接有哪些优点\|长连接]]
	- 减少握手的次数，提高传输效率
2. 支持管道（Pipeline）传输
	- 可以连续发送多个请求
3. **更灵活的缓存策略**，新增以下 Header：
	- E-tag
	- If-Unmodified-Since
	- If-Match
	- If-None-Match
3. 支持通过 [Range](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Range) 来**断点重传**
4. 增加了 Host 头
	- 可以让一台主机**多个**站点，共享同一个 IP 地址。
