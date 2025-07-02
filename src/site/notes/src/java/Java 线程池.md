---
{"dg-publish":true,"dg-permalink":"Java 线程池","permalink":"/Java 线程池/"}
---


#Java #Java基础

## 为什么会有线程池？
    
1. 降低**资源的消耗**：不用频繁创建和销毁
2. 提高**响应速度**：任务到达时不用等待创建
3. 提升线程的**可管理性**：统一分配、统一管理、统一监控
4. 提供更多**更强大的功能**：如 *ScheduledExecutorService*
	
## 创建线程池都有哪些参数？

1. corePoolSize：**核心**线程数
2. maximumPoolSize：**最大**线程数
3. keepAliveTime：非核心线程的存活时间
4. unit：keepAliveTime 的时间**单位**
5. workQueue：[[src/java/JUC#BlockingQueue 的实现原理？\|阻塞队列]]接口具体实现的对象
	- 内部线程池，用于任务提交和执行的解耦
	- 当核心线程满时，用于任务的缓冲
6. threadFactory：创建线程工厂
7. handler：拒绝策略处理器

## 核心线程和最大线程数的区别？

1. 核心线程：线程池承载日常任务的**中坚力量**，正常预估的线程数量
2. 最大线程：为了应付**突发状况**
3. 回收策略
	- *allowCoreThreadTimeOut* 默认为 *false* 的情况下，核心线程将会永远不会被回收
	- 而非核心线程在 *keepAliveTime* 时间内如果还**未获取到任务**，将会被销毁

## 线程池都有哪几种状态？

1. RUNNING
	- 能接受新任务
	- 并处理阻塞队列中的任务
2. SHUTDOWN
	- 不接受新任务
	- 但是可以处理阻塞队列中的任务
3. STOP
	- 不接受新任务
	- 并且不处理阻塞队列中的任务，且还打断正在运行任务的线程
4. TIDYING
	- 所有任务都终止
	- 并且工作线程也为 0，处于关闭之前的状态
5. TERMINATED
	- 已关闭

## 线程池中都有哪些拒绝策略的？

1. AbortPolicy（默认）
	- 直接抛出 *RejectedExecutionException* 异常
2. CallerRunsPolicy
	- 直接在当前线程执行
3. DiscardPolicy
	- 直接忽略异常
4. DiscardOldestPolicy
	- 从队列中删除最旧的任务后，再执行
  
## 如何实现一个在当前线程阻塞的拒绝策略？

1. 通过 `executor.getQueue()` 获取到线程池的阻塞队列
2. 调用 `put` 方法将任务提交到队列中，如果**队列满**则会被阻塞，直到队列不为满时才会被唤醒

参考：[BlockingRejectedExecution.java](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/builtin/concurrent/threadpool/BlockingRejectedExecution.java)
  
## 如何简单手写一个线程池？

关键：
1. [[src/java/JUC#BlockingQueue 的实现原理？\|阻塞队列]]
2. 线程集合
3. 线程循环从阻塞队列中取出任务
4. 暴露提交执行任务的 API：将任务 *Runnable* 对象放入到阻塞队列中

参考：[SimpleThreadPool.java](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/builtin/concurrent/threadpool/SimpleThreadPool.java)
	
## 线程池可以动态修改核心线程数和最大线程数吗？

*ThreadPoolExecutor* 本身有相应的 API：

1. setCorePoolSize：更新核心线程数
2. setMaximumPoolSize：更新最大线程数
3. setKeepAliveTime：更新 KeepAlive
4. 阻塞队列不支持设置

## 原生线程池的核心线程一定伴随着任务慢慢创建的吗？

并不是，线程池提供了两个方法，可以用来做预热：
1. *prestartCoreThread*：启动**一个**核心线程
2. *prestartAllCoreThreads* ：启动**所有**核心线程

## 线程池参数如何设置？

比如：
- coreSize：`2 * CPU 的核数`
- maxSize：`25 * CPU 的核数`

但是没有考虑**使用多个线程池**的情况，统一配置不合理
    
## 线程个数如何设置?

1. CPU 密集型（很少有阻塞发生）
	- 降低线程池，避免频繁的线程切换，提高 CPU 的利用率
	- 公式：**CPU 核数 + 1**（+ 1 是备用线程）
2. IO 密集型（I/O 操作是不占用 CPU 资源的）：
	- 避免 I/O 操作时，CPU 空闲，增大线程个数
	- 公式
		- 单核： 最佳线程数 = **1 +（I/O耗时 / CPU耗时）**
		- 多核： 最佳线程数 = **CPU核数 * [ 1 +（I/O耗时 / CPU耗时）]**

> [!INFO] 
> 假设 CPU 和 I/O 时间耗时 1：1，那么单核下最佳线程数 = 1 + 1 = 2
> 
> 这样 I/O 和 CPU 就可以交互执行了，利用率都达到 100%。
    

## 参考资料

> [!QUOTE] 
> [两道面试题，深入线程池，连环17问](https://mp.weixin.qq.com/s/NDOx94yY06OnHjrYq2lVYw)