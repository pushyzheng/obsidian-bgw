---
{"dg-publish":true,"dg-permalink":"Java 并发相关","permalink":"/Java 并发相关/"}
---


#Java 

## 并发的起因？

1. **可见性**
	- 多核 CPU 时代，每个 CPU 核心都是用自己的缓存
	- JMM 中每个线程都是操作自己的工作内存
2. **原子性**
	- 线程上下文切换（CPU 时间片耗尽）
3. **有序性**
	- 编译优化，指令重排
	- CPU 的指令重排

## 什么是竞态条件？

竞争条件（*Race condition*）即：

- 当**多个**进程都企图对共享数据进行某种处理
- 而最后的结果又取决于进程**运行的顺序**时

## 为什么 wait 和 notify 方法要在同步块中调用?

Java 强制这么做的，如果调用 wait 时，没有获取到这个对象的锁的话，会抛出 *IllegalMonitorStateException* 异常。

目的：防止 `wait` 和 `notify` 方法产生竞态竞争。

参考：[为什么 wait 要放在 synchronized 里边？](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E4%B8%BA%E4%BB%80%E4%B9%88%20wait%20%E8%A6%81%E6%94%BE%E5%9C%A8%20synchronized%20%E9%87%8C%E8%BE%B9%EF%BC%9F)

## 为什么你应该在循环中检查等待条件?

为了防止出现虚假唤醒（*spurious wakeup*），但实际并不满足状态，而会导致程序退出。

参考：[Java 线程同步](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20%E7%BA%BF%E7%A8%8B%E5%90%8C%E6%AD%A5)

## notify()和 notifyAll()有什么区别?

- notifyAll()（**推荐**）：唤醒**所有的**等待的线程，重新开始争抢锁；
- notify()：唤醒等待线程中的**随机一个**线程；

## 什么是 CAS？它会存在什么问题？

[CAS](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FCAS) 即比较并交换，存在的问题有：
1. [ABA](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FABA%20%E9%97%AE%E9%A2%98)：引入版本号的机制，可使用 *AtomicStampedReference* 解决
2. 自旋空转：限制自旋的次数，并在获取锁失败进行 yield
3. 只能控制一个变量：使用 AtomicReference 封装到一个对象中

## 什么是 Happens-Before 原则？

约束编译器的优化行为，要求编译器优化必须遵循该原则，从而保证程序的**可见性**。

（`→` 表示对其可见）

1. 程序顺序性原则
	- 前面的代码 → 后边的代码
2. volatile 遍历原则
	- 对 volatile 遍历的写操作 → 读操作
3. 传递性
	- A → B、B → C，则 A → C
4. 管程锁定原则
	- 解锁之前做的操作 → 其他线程再次加锁时的操作
	- 所以 *synchronized* lock 前会**清空工作内存**，unlock 也会把最新数据**同步到主内存**
5. 线程 start 原则
	- 主线程启动子线程前的操作 → 子线程启动后
6. 线程 join 原则
	- 子线程执行时的操作 → 主线程 join 等待线程 B 完成时
