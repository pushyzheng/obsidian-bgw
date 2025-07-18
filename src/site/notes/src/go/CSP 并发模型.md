---
{"dg-publish":true,"dg-permalink":"CSP 并发模型","permalink":"/CSP 并发模型/"}
---


## 什么是 CSP 并发模型？

Golang 的并发模型基于 CSP（Communicating Sequential Processes） 理论，该模型通过 [[src/go/Go 协程#go 协程（Goroutine）与传统线程有什么区别？\|Goroutine]] 和 [[src/go/Go 并发#什么是 channel？\|Channel]] 来实现，强调：

> [!NOTE] 
> 通过**通信**来共享内存，而不是通过共享内存来通信

概念：不同并发进程(Goroutine)通过发送和接收消息来进行交互，而不是通过共享变量。

## CSP 模型的核心原则

1. 通过通信共享内存
	- 通过 channel 来进行通信，避免多协程访问共享内存引发的竞争条件
	- 减少了数据竞争和锁的使用
2. 同步机制
	- channel 提供天然的同步机制，这种机制避免了手动使用锁来同步数据

## CSP 并发模型的优点

- 简化并发编程
	- 通过 Goroutine 和 Channel，使并发编程更加直观，不需要直接处理锁和条件变量
- 安全性
	- 降低了并发操作中数据竞争和死锁的风险
- 可伸缩性
	- Goroutine 非常轻量级，模型具有高可伸缩性