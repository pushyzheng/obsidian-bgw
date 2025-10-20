---
{"dg-publish":true,"dg-permalink":"CMS 常见问题","permalink":"/CMS 常见问题/","tags":["JVM"]}
---


#Java #JVM

CMS 即 *Concurrent Mark-Sweep*，使用**并发标记**收集算法。

## CMS 收集的过程？

除了第一阶段，都是多线程执行：

1. 初始标记（STW）
	- 标记 GC Roots 能**直接关联**到的对象
2. 并发标记（**并发**）
	- 进行递归追踪的过程，标记**所有可达**（间接关联）的对象
3. 重新标记（STW）
	- 修正在第二阶段时，用户线程运行产生新的引用关系和对象，所以需要 STW
4. 并发清除（**并发**）
	- 清除 GC Roots 不可达的对象

## CMS 收集器的优缺点有哪些？

1. 优点
	- 到达 GC 线程和用户线程的**并发执行**
	- 较低的停顿
2. 缺点
	- 会产生**内存碎片**：标记清除算法所带来的影响
	- 无法处理**浮动垃圾**：在并发清除阶段（4）产生的垃圾（因为是和用户并发执行）
	- 对 CPU 资源敏感：在并发阶段会占用 CPU 资源

## 内存碎片会导致什么问题？

![](/img/user/attachments/images/Pasted image 20220325150255.png)
内存碎片会导致：
1. 空间**分配效率**较低
	- 增大遍历 freelist 的次数
2. 空间**利用效率**变低
	- 当大对象所需地址空间 > 最大的连续空间时
	- 将导致 **Promotion Failed**，即使剩余空间 > 所需地址空间。

## 内存碎片解决方案

1. 通过配置 `-XX:UseCMSCompactAtFullCollection = true`，控制 **Full GC** 的过程中是否进行**空间的整理**（默认开启）
	- 该过程无法并发， STW 会变长
2. `-XX: CMSFullGCsBeforeCompaction = N`，控制 N 次 Full GC 后**进行一次压缩**
3. 调整 `-XX:CMSInitiatingOccupancyFraction`，来降低 CMS GC **触发的阈值**

## 在哪些情况下 CMS 收集器会发生退化？

CMS 在以下几种情况时，会退化为单线程的 *Serial GC* 进行一次 **FullGC**，STW 时间比较长。

1. 晋升失败（*Promotion Failed*）：进行 YoungGC 的时候，Survivor 放不下，只能放入 Old，但此时 Old 也放不下，通常是因为 CMS 的**内存碎片**导致的
2. 增量收集担保失败
3. 显式 GC：调用 *System.gc()*
4. 并发模式失败（*Concurrent Mode Failure*）：发生**概率较高**，即：
	- CMS 在并发收集的过程中
	- 又有 Young GC 晋升的对象要放入 Old 区中（因为 **CMS 无法处理浮动垃圾**）