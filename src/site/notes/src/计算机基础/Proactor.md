---
{"dg-publish":true,"dg-permalink":"Proactor","permalink":"/Proactor/"}
---


## 什么是 Proactor 网络模式？

Proactor 是异步网络模式，核心思想：
1. 应用程序发起**异步I/O**操作后，不需要等待数据准备和拷贝完成
2. 操作系统会**自动**异步完成数据从内核态到用户态的拷贝
3. 在操作完成后**通知**应用程序处理数据

![Pasted image 20250724172224.png](/img/user/attachments/images/Pasted%20image%2020250724172224.png)

## Reactor 和 Proactor 的区别?

1. ==[[src/计算机基础/Reactor\|Reactor]]==
	- **非阻塞同步**网络模式
	- 感知的是**就绪**可读写事件
2. ==Proactor==
	- **异步**网络模式: 不需要像[[src/计算机基础/Reactor\|Reactor]]需要应用程序**主动**发起 read/write 读写数据
	- 感知的是**已完成**的读写事件