---
{"dg-publish":true,"dg-permalink":"Java 线程池底层实现","permalink":"/Java 线程池底层实现/"}
---


#Java 
	
## 组成部分

底层设计是**生产者消费者模型**：

1. Entry API 入口：提交任务并入队
2. workQueue（[[src/java/JUC#BlockingQueue 的实现原理？\|阻塞队列]]）
	- 缓冲：核心线程占满时，任务将入队
	- 解耦：队列
	- 不同的调度策略：如 *PriorityBlockingQueue* 支持**高优先级**的调度策略
3. workers：出队并执行

## 任务的提交过程

1. 提交任务
2. 判断
	- 如果 Worker 数量**小于**核心线程数
		- 创建新的**核心线程**并直接执行任务
	- 如果 Worker 数量**大于**核心线程数
		- 将任务入队
		- 如果队列满，则创建**非核心线程**执行
	- 如果超过最大线程数，触发拒绝策略

## 非核心线程过期回收机制？

Worker 调用 getTask 获取执行任务时，判断：

```java
// allowCoreThreadTimeOut 默认为 false
// 为 true 代表核心线程也会在超时后被销毁
boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;
```

1. `timed = true`（存在非工作线程）：从阻塞队列中获取任务调用 `workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS)`，如果该事件内没有其他任务将会超时退出，即被销毁
2. `timed = false`：从阻塞队列中获取任务调用 `workQueue.take()`，会进行阻塞，线程不会退出