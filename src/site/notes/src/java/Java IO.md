---
{"dg-publish":true,"dg-permalink":"Java IO","permalink":"/Java IO/"}
---


#Java #Java基础 

## 字节流和字符流的区别？

- 字节流，基本单位为字节，采用 **ASCII** 编码；
	- InputStream
	- OutputStream
- 字符流：其底层仍然为字节流，采用 **Unicode** 编码（默认的具体实现为 UTF-8）
	- Reader
	- Writer

详细参考：
- [流的区别](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20IO)
- [字节和字符的区别？](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Fconcept%2F%E5%AD%97%E8%8A%82%E5%92%8C%E5%AD%97%E7%AC%A6%E7%9A%84%E5%8C%BA%E5%88%AB)

## 什么是 I/O？

I/O 就是指**内存**与**外部设备**之间的交互，即数据的拷贝：
1. 网络 I/O ：**网卡**与内存之间的输入输出
2. 磁盘 I/O ：**磁盘**和内存之间的输入输出

I/O 会存在两个阶段：

-   **等待就绪（可读/可写）**：等待字节流可读取/可写入
-   **数据拷贝**：真正开始内核态与用户态之间的数据拷贝

## 五种 I/O 模型？

[五种 I/O 模型](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E4%BA%94%E7%A7%8D%20IO%20%E6%A8%A1%E5%9E%8B)区别如下：

| Name         | 等待就绪 | 数据拷贝 | 特点                           |
| ------------ | -------- | -------- | ------------------------------ |
| 阻塞 I/O     | **阻塞** | 同步     | -                              |
| 非阻塞 I/O   | 非阻塞   | 同步     | 非阻塞                         |
| I/O 复用     | 非阻塞   | 同步     | 多路复用，单一线程监听多个连接 |
| 信号驱动 I/O | 非阻塞   | 同步     | 信号驱动                               |
| 异步 I/O     | 非阻塞   | **异步** | 真正的异步                               |

## BIO、NIO、AIO 有什么区别？

传统 I/O 和 NIO 之间的区别：

| 传统 I/O                  | NIO                    |
| -------------------- | ---------------------- |
| 面向**流**（Stream） | 面向**缓冲**（Buffer） |
| 阻塞                 | **非**阻塞                 |
| -                    | 有 Channel 和 Selector。<br>允许用**一个**线程来监听**多个**通道（客户端连接读写事件）                       |

1. BIO（*Blocking I/O*，同步阻塞 I/O）：
	- **编程模型简单**：让单个线程专注于一个连接，有更高的开发效率和可维护性
	- **性能瓶颈**：无法应对高并发的场景
2. NIO（*Non-Blocking I/O*，同步非阻塞 I/O），从 JDK1.4 引入的：
	- **同时支持**：阻塞和非阻塞两种模式；
	- **面向缓冲**：并抽象出 *Buffer*、*Channel*、*Selector* 三大组件
	- **减少内存复制**：
		- *DirectBuffer*（堆 → 直接内存）
		- *MapperedByteBuffer*（零拷贝，内核态 → 用户态）
	- 节省资源：节省出更多的 **CPU 资源**，更好地应对高并发的场景
	- 缺点：**编程难度比较高**，但是可以基于高级封装的框架开发（Netty、Reactor）
3. [AIO](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20AIO)（*Asynchronous I/O*）即五种模型的**异步 I/O** 的实现，在 JDK7 后引入。
	- 异步非阻塞模型：I/O 的两个阶段都不会有阻塞发生；
	- 基于事件和回调机制
	- 应用不是很广泛

## NIO 有哪些核心组件

1. [Buffer](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJav%20NIO%20-%20Buffer)：即缓冲区
2. [Channel](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20NIO%20-%20Channel)：即通道，类似于 Stream，**用于读取或者写入数据**，是访问 Buffer 的接口；
3. [Selector](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20NIO%20-%20Selector)：即选择器，用于单个线程监听**多个**客户端的连接、可读、可写事件，是非阻塞的核心
