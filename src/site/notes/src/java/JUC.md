---
{"dg-publish":true,"dg-permalink":"JUC","permalink":"/JUC/"}
---


#Java #JUC

https://mp.weixin.qq.com/s/mPB-hX6g_S_Ifld7ku7xUQ

## ConcurrentHashMap 是如何实现的？

区别：

| 功能     | 1.7           | 1.8                      |
| -------- | ------------- | ------------------------ |
| 数据结构 | 数组 + 链表   | 数组 + 链表 + **红黑树** |
| 并发实现 | 分段锁        | [CAS](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FCAS) + 同步代码块         |
| 锁的粒度 | **段**（*Segment*） | 哈希槽的**头结点**（*Node*）                         |

1. [JDK 1.7](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FConcurentHashMap%20-%20JDK7)
	- **锁细粒度化**：使用分段锁，来提高效率；
	-  **Unsafe 类**操作：极致的性能优化；
	- **保证可见性**：Entry 的 value 被 volatile 修饰。
2. [JDK 1.8](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FConcurentHashMap%20-%20JDK8)
	- 引入了**懒加载**模式
	- **进行了树化**：和 HashMap 一样，在链表长度大于 8 后变成红黑树；
	- **降低了锁的粒度**：对**头结点**使用 *synchronized* 加锁，这样随着元素的增多，并发度也不会降低。
	- **使用 CAS 操作**：来完成设置哈希槽结点等操作，提高了效率。

## ConcurrentHashMap 1.8 中为什么放弃分段锁？

1. 由于 *Segment* 数组**不会扩容**
	- 会一直保持 16 个
	- 当元素增加时，每个 Segment 存储的数据增多时，锁的粒度就变大了，**并发度就会降低**。
2. 分成多端时，**比较浪费内存空间**
	- 不连续，碎片化

从 *ReentrantLock* 替换到 *synchronized* 的原因是：
1. **减少内存开销**：AQS 本身存在一定的内存消耗（例如内部的阻塞队列等）
2. synchronized 的性能**已经被优化**的不错了

## ConcurrentHashMap 1.7 和 1.8 中 size 方法有什么不同？

| 版本 | 实现 |
| ---- | ---- |
| 1.7  | 乐观重试（3 次）     |
| 1.8  | LongAdder     |

### 1.7 实现

先乐观统计，当重试次数超过 **3 次**时，再升级为悲观：

1. 先不对所有 Segment 加锁，累加所有 Segment 中 **count**（乐观）
2. 判断在统计期间没有修改记录（通过 **modCount** 来判断）
	- 没有：完成统计
	- 有：重试
3. 当重试次数**大于 3 次**时，对**所有的 Segment 加锁**，完成统计（悲观）

### 1.8 实现

1.8 中是通过 *CounterCell* 来实现的：

```java
@sun.misc.Contended static final class CounterCell {  
	volatile long value;  
}
```

在执行 [sumCount](https://jihulab.com/learning/interview/-/blob/main/com/jdk/ConcurrentHashMap18.java#L2511) 时，只是简单地遍历每个  CounterCell，再累加 baseCount 即可。

实现原理是，每次 *putVal* 等更新操作**都会 CAS 更新 baseCount 值**
- 如果成功：直接返回
- 如果失败：存在并发竞争，利用 hash 选择一个 CounterCell 进行修改

![](/img/user/attachments/images/Java-CounterCell.png)

## ConcurrentHashMap 读需要加锁吗？

不需要。

因为 *ConcurrentHashMap* 都被 *volatile* 修饰，避免了出现数据不一致的情况，有：
1. 底层的哈希表数组 **table**
2. 以及 Node 对象的 **val**  和  **next**  属性

```java
transient volatile Node<K,V>[] table;

static class Node<K,V> implements Map.Entry<K,V> {  
	
	final K key;
	volatile V val;  
    volatile Node<K,V> next;
}
```

## HashMap 、 HashTable 、ConcurrentHashMap 的区别？

- HashTable 、ConcurrentHashMap 是线程安全，**HashMap 不是**；
- HashTable 、ConcurrentHashMap 不允许有任何空的键和值；**HashMap 允许有一个空键和多个空值**；
- HashTable 比 HashMap 更慢，因为被方法被 *synchronized* 修饰。

## BlockingQueue 的实现原理？

1. 在 Queue 基础上新增两个 API
	- 出队：在队列为空时，直到（阻塞或超时）**等待队列不为空**
	- 入队：在队列满时，直到（或超时）**等待队列不满**
2. 实现原理参考：[BlockingQueue](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FBlockingQueue)，主要原理
	- 一个 ReentrantLock 加锁保证并发
	- 两个 Condition （**NotFull、NotEmpty**）控制两个操作
3. 实现：
	- ArrayBlockingQueue：数组实现的**有界**队列
	- LinkedBlockingQueue：**链表**实现的无界队列
	- PriorityBlockingQueue：具有**优先级**的无界队列
	- SynchronousQueue：**不存储实际数据**的队列
	- LinkedTransferQueue：链表结构的无界队列

## COW 可以用于什么应用场景?

实现见：[CopyOnWriteArrayList](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FCopyOnWriteArrayList)

1. 原理：
	- 通过修改副本数据，再覆盖原数组的方式来保证并发修改的**最终一致性**
2. 缺点
	- 数组量级大：**容易引发 youngGC**，甚至 FullGC
	- 不适用**实时读**的场景：只能保证最终一致性
	- 写性能并不高：依然会有全局锁
3. 适用场景
	- 读多写少：尽量避免频繁的数组拷贝
	- 对一致性要求不高：可以达到最终一致性的

## 在 Java 中 CyclicBarrier 和 CountdownLatch 有什么区别?

1. *CountdownLatch* 
	- 让**一个线程**等待 N 个线程完成某件事之后，才能执行
	- 当计数减至零时，位于 *latch.await()* 后的代码才会被执行
	- **不能够重用**
2. *CyclicBarrier*：
	- 用于**一组线程**互相等待至某个状态，然后这一组线程再同时执行
	- 可以重用的