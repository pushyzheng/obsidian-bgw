---
{"dg-publish":true,"dg-permalink":"AQS","permalink":"/AQS/","tags":["JUC"]}
---


#Java #JUC 

## AQS 的实现原理？

1. **state** 表示状态的原子值
	- 可见性：被 volatile 修饰
	- 原子性：修改操作是通过 CAS 来完成
2. **等待队列**
	- 用来维护申请资源失败，进入等待的线程集合
3. **模板方法**设计模式
	- AQS 本身完成了核心的等待队列、线程休眠与唤醒等逻辑
	- 定义 protectd 方法让子类来完成对 state 的修改操作，实现不同的同步器
4. 可以创建**多个** Condition 

waitStatus：
- PROPAGATE（-3）：共享状态
- CONDITION（-2）：等待队列
- SIGNAL（-1）：唤醒
- CANCELLED（1）：取消

## AQS 都有哪些实现？

1. 互斥锁
	- ReentrantLock
	- ReentrantReadWriteLock
2. 共享锁
	- Semaphore
	- CountdownLatch

## ReentrantLock 的实现过程

1. 内部会有 *FairSync* 和 *NonfairSync* 公平和非公平实现，继承自 AQS
2. 实现了 *tryAcquire* 方法，在加锁时：
	- 如果 state = 0 且 CAS 成功：加锁成功，`state = 1`
	- 否则判断申请锁的线程是否为持有锁的线程：
		- 不是：申请锁失败
		- 是：`state++`
3. 如果申请锁失败，将入队

## AQS 的阻塞和唤醒过程是怎样的？

当 *tryAcquire* 失败时，将会调用 *addWaiter* 进行入队，追加到尾结点，然后调用 *acquireQueued* 来尝试获取锁：

1. 判断**前驱**是否为头结点（第一个有效结点）
	- 如果是：调用 *tryAcquire* 尝试获取锁
		- 如果抢到就将 head 设为自己
		- 如果失败：跳到 2
	- 如果不是：跳到 2
2. 调用 *shouldParkAfterFailedAcquire*
	- 将**前一个节点**的 *waitStatus* 设置为 **-1**（代表告诉前一个结点，如果释放锁时，将我唤醒）
	- 如果是取消状态（`ws > 0`），则**向前查找**，直到第一个 `ws < 0`（并删除这些结点）
3. *LockSupport.park* 进行**挂起**

## AQS 是如何来实现公平和非公平的呢？

公平锁的最大区别在于会调用 *[hasQueuedPredecessors](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FAQS%20%E5%85%AC%E5%B9%B3%E5%92%8C%E9%9D%9E%E5%85%AC%E5%B9%B3%E7%9A%84%E5%AE%9E%E7%8E%B0)* 方法，判断等待队列中**是否有节点**在排队：
1. 从**第二个**结点开始（因为第一个节点是虚节点）
2. 如果 `h.next == null`，则代表有结点在等待
3. 或者等待线程不是自己（`s.thread != Thread.currentThread()`）