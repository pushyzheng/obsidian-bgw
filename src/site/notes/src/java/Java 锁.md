---
{"dg-publish":true,"dg-permalink":"Java 锁","permalink":"/Java 锁/"}
---


#Java 

## JDK 锁的分类有？

1. 乐观锁 & 悲观锁
2. 独享锁 & 共享锁
3. 互斥锁 & 读写锁
4. 可重入锁
5. 公平锁 & 非公平锁
6. 分段锁
7. 偏向锁 & 轻量级锁 & 重量级锁

## JDK 中都有哪些锁实现？

1. *synchronized*：由虚拟机底层通过 ObjectMonitor 来实现的
2. Lock
	- ReentrantLock：可重入锁，支持公平和非公平
3. ReadWriteLock
	- 读写锁
	- 适用于读多写少的场景，如本地缓存
4. StampedLock
	- 同时支持悲观读写锁和乐观读锁（*validate* 校验数据是否被修改）

## Synchronized 与 Lock 相比优缺点分别是什么？

1. *Synchronized*
	- 优点：
		- 无需手动释放
		- 更轻量级
	- 缺点：
		- 无法尝试加锁和超时等待，扩展性不高
2. *Lock*
	- 优点：
		- 同时支持公平锁和非公平锁
		- 支持**多条件变量**（*Condition*）
		- 可以中断或设置**超时等待**
		- 具有更丰富的特性：如读写锁、乐观锁等
	- 缺点：
		- 需要占用更多的内存