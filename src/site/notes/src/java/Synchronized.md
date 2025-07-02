---
{"dg-publish":true,"dg-permalink":"Synchronized","permalink":"/Synchronized/"}
---


#Java 

## Synchronized 关键字底层是如何实现的?

[Synchronized](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2Fsynchronized%20%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86) 实现原理：
1. 底层 Hotspot 的 **ObjectMonitor** 即 Monitor 对象来实现互斥，Java 中的**每个对象**都具有一个
2. 在对象头中的 **Mark Word** 中会存储轻量级锁、重量级锁（指向 Monitor 对象地址）的信息

*ObjectMonitor* 中关键的属性：

```c
ObjectMonitor() {
    _recursions   = 0;       // 重入次数
    _object       = NULL;    // 存储该 monitor 的对象
    _owner        = NULL;    // 锁的拥有者
    _WaitSet      = NULL;    // 阻塞等待线程的链表
	...
}
```

## Synchronized 的升级过程？

1. 无锁
	- hashCode、分代年龄等
2. 偏向锁（[Java 15+ 被废弃](https://openjdk.org/jeps/374)）
	- 偏向的**线程 ID**
	- 加锁过程：使用 **CAS** 修改 *Mark Word* 的线程 ID
3. 轻量级锁
	- 栈中锁记录（*Lock Record*）的指针
	- 加锁过程
		- 在栈中创建 Lock Record
		- CAS 修改 *Mark word* 为 **Lock Record 地址**
	- 缺点：多线程竞争时，会引起过多**自旋空转**
4. 重量级锁
	- 底层 ObjectMonitor **对象的地址**
	- 加锁过程：底层 Monitor 对象实现
	- 缺点：阻塞唤醒需要**系统调用**，性能开销比较大