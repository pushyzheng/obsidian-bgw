---
{"dg-publish":true,"dg-permalink":"HTTP2","permalink":"/HTTP2/"}
---


#计算机网络 #HTTP

## HTTP 2 协议有哪些优点?

针对 HTTP **性能**的改进，整体**传输效率**得到了很大的提升
1. ==二进制分帧==
	- 把一个请求拆分成多个很小的数据包（帧），采用**二进制**形式进行传输
	- **多个**请求拆成许多数据包一起发送，接收端再根据序号进行拼接
	- 增加了数据传输的效率
2. ==并发传输==
	- 多路复用: 多个 Stream 复用在一条 TCP 连接（减少连接的建立）
	- 好处：解决 HTTP 1.1 的**队头阻塞**问题
3. ==头部压缩==
	- 降低报文大小，提高传输速度
	- HTTP 1.1 只能对**请求体**进行 gzip 压缩
	- 大部分的请求中请求头的信息**都是重复**的，以此增快传输效率
	- 实现：通过[[src/计算机基础/HTTP 2#HPACK 编码\|HPACK编码]]来压缩长文本的数据
		- 静态表
		- 动态表
4. ==数据推送==
	- 解决请求只能从客户端开始的问题
5. ==Stream 优先级==
	- 不同请求资源的优先级，例如可以控制框架的静态资源优先加载，而图片则延迟
	- 父子依赖、权重
	
## HTTP 1.1 长连接和 HTTP 2 的多路复用区别

![640.png|500](/img/user/attachments/images/640.png)

## HPACK 编码

![Pasted image 20220305193543.png|500](/img/user/attachments/images/Pasted%20image%2020220305193543.png)