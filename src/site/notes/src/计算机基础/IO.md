---
{"dg-publish":true,"dg-permalink":"IO","permalink":"/IO/"}
---


#操作系统 

## 一共有多少中 I/O 模型？

[I/O](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FIO) 有两个阶段：
1. 等待就绪：等待可读可写
2. 数据拷贝：内核态与用户态之间的数据拷贝，会发生阻塞

[I/O 模型一共有五种](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E4%BA%94%E7%A7%8D%20IO%20%E6%A8%A1%E5%9E%8B)：

| 模型\阶段  | 第一阶段 | 第二阶段 |
| ---------- | -------- | -------- |
| 阻塞 I/O   | 阻塞     | 阻塞     |
| 非阻塞 I/O | 非阻塞   | 阻塞     |
| 多路复用   | 阻塞   | 阻塞     |
| 事件驱动   | 非阻塞   | 阻塞     |
| 异步 I/O   | 非阻塞   | 非阻塞   |

## 什么是 I/O 多路复用？

I/O 多路（网络连接）复用（同一个线程）是一种**同步** I/O 模型

核心思想：**复用线程**资源，而不是为每个连接创建独立线程或进程

- 允许**单个**线程可以监视**多个**文件描述符（socket连接）
- 一旦某个文件描述符**就绪**（可读可写），就会通知应用程序处理，实现高效并发处理



## 都有哪些 I/O 多路复用的算法，及实现原理？

[[src/计算机基础/IO 多路复用算法\|IO 多路复用算法]]

## Reactor 有哪三种线程模型？

1. [Reactor](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FReactor%20%E6%A8%A1%E5%BC%8F) 组件
	- *Acceptor*： 处理客户端连接请求
	- *Reactor*：将 I/O 事件**派发给 Handler**
	- *Handlers*：执行非阻塞读/写操作
2. 三种模型
	- 单线程
		- 所有的 I/O 操作都在**一个**线程（共享）
		- 在高负载下，整体会有很大的**性能瓶颈**
	- 多线程
		- 对 *Handlers* 进行了**池化**，但 *Acceptor、Reactor* 仍共用一个线程
		- 当连接数量更多时，**Acceptor** 会存在性能瓶颈
	- 主从
		- 对 Acceptor 池化，提升处理连接的效率

现代大多数的 NIO 框架都是基于**主从模型**，如 Netty、Redis。