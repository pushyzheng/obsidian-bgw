---
{"dg-publish":true,"dg-permalink":"Reactor","permalink":"/Reactor/"}
---


## Reactor 线程模型的组成？

Reactor 组件
- ==Acceptor==：
	- 作用是获取连接
	- 处理客户端**连接请求**
- ==Reactor==
	- 负责监听和分发事件
	- 将 I/O 事件**派发**给 Handler
- ==Handlers==
	- 作用是处理业务
	- 执行非阻塞**读/写操作**

## Reactor 有哪三种线程模型？

三种模型
1. ==单线程==
	- 所有的 I/O 操作都在**一个**线程（共享）
	- 在高负载下，整体会有很大的**性能瓶颈**
2. ==多线程==
	- 对 Handlers 进行了**池化**，能够充分利用多核 CPU 的能
	- 但 Acceptor、Reactor 仍**共用一个线程**
	- 当连接数量更多时，Reactor/Acceptor 仍然会存在性能瓶颈
3. ==主从模型==
	- 对 Acceptor **池化**，提升处理连接的效率
	- 主线程只负责接收新连接，子线程负责完成后续的业务处理

现代大多数的 NIO 框架都是基于**主从模型**，如 Netty、Redis。