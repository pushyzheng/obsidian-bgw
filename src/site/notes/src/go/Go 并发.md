---
{"dg-publish":true,"dg-permalink":"Go 并发","permalink":"/Go 并发/","tags":["Go"]}
---


#Go

## 什么是 channel？

1. 定义：设计于Goroutine之间的**通信**的机制
2. 特点
	- **并发安全**：通过**通信**来共享内存，可以在不同协程间，存储和传递数据
	- 先进先出：底层数据结构是**环形队列**
	- 支持阻塞和非阻塞
3. 同步和阻塞
	- **无缓冲**（未指定大小）
		- 发送者：chan 无人接收
		- 接受者：chan 没有数据
	- **带缓冲**（指定大小）
		- 发送者：chan 已满
		- 接收者：chan 没有数据

## Context 是什么？

1. 定义
	- 是 Go 中应用开发常用的**并发控制**技术
	- 每个 goroutine 拥有相同的上下文
2. 作用：用于控制多个协程之间的协作、取消操作
3. 方法
	- Deadline：获取设置的**截止时间**
	- Done：返回只读 chan，可以读取时代表**被取消**
	- Err：返回被取消的**原因**
	- Value：获取绑定在 Context 上的**键值对**

## Context 有什么作用？

1. **控制超时/取消**：可以用来管理请求的生命周期
2. **传递值**：跨函数、跨协程传点小数据（比如 traceID）
3. **协程管理**：多个 Goroutine 一起跑，需要一起停，就靠 Context 通知

## Go 中的锁都有哪些？

1. sync.Mutex
	- 互斥锁，防止竞态（资源竞争）
	- **不可重入的**
2. sync.RWMutex
	- 读写锁
	- 提高了对共享资源的**访问效率**
3. sync.Once
	- 只**执行一次**，用来做单例的初始化
4. sync.WaitGroup
	- 同步等待一组 Goroutine 结束

## 原子操作的支持都有哪些？

在 *sync/atomic* 包中，提供了对**原子操作**的支持，用于同步访问整数和指针。

有：
1. CompareAndSwap（CAS）：传递 addr、old、new
2. Swap：传递 addr、new，返回 old
3. Add：传递 addr、delta
4. Load：传递 addr
5. Store：传递 addr、val