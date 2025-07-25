---
{"dg-publish":true,"dg-permalink":"Java 并发相关","permalink":"/Java 并发相关/"}
---


#Java 

## 并发的起因？

1. ==可见性==
	- **多核** CPU 时代，每个 CPU 核心都是用自己的**缓存**
	- JVM 中每个线程都是操作自己的**工作内存**
2. ==原子性==
	- 当 CPU 时间片耗尽，会导致线程**上下文切换**
3. ==有序性==
	- 编译优化，**指令重排**
	- CPU 的指令重排

## 为什么 wait 和 notify 方法要在同步块中调用?

- 如果调用 wait 时，没有获取到这个对象的锁的话，会**抛出异常**
- 目的：防止 `wait` 和 `notify` 方法产生[[src/计算机基础/竞争条件\|竞态竞争]]

## 为什么你应该在循环中检查等待条件?

为了防止出现虚假唤醒（*spurious wakeup*），但实际并不满足状态，而会导致程序退出。

## notify()和 notifyAll()有什么区别?

- notifyAll()（**推荐**）：唤醒**所有的**等待的线程，重新开始争抢锁；
- notify()：唤醒等待线程中的**随机一个**线程；

## 什么是 CAS？它会存在什么问题？

[CAS](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FCAS) 即比较并交换，存在的问题有：
1. [ABA](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FABA%20%E9%97%AE%E9%A2%98)：引入版本号的机制，可使用 *AtomicStampedReference* 解决
2. 自旋空转：限制自旋的次数，并在获取锁失败进行 yield
3. 只能控制一个变量：使用 AtomicReference 封装到一个对象中

## 什么是 Happens-Before 原则？

[[src/java/Happens-Before 原则\|Happens-Before 原则]]