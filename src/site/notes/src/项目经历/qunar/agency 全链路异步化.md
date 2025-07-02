---
{"dg-publish":true,"dg-permalink":"agency 全链路异步化","permalink":"/agency 全链路异步化/"}
---


#项目 

## 为什么使用 Reactor ？

1. 为什么要使用全链路异步化
{ #b09156}

	- QPS 较高
		- 查询报价接口 QPS 在 **20K ~ 30 K** 之间，需要具备高吞吐量
	- I/O 密集型任务较多
		- 有大量的请求外部代理商 HTTP 接口和内部的 Redis 请求
		- 有些代理商接口**响应速度慢**
	- 响应时间敏感
		- 需要尽可能地减少报价的返回时间
2. NIO 有什么好处？
	- 优点
		- 非阻塞 I/O，使用更少线程（硬件资源）来处理大量并发请求
		- 内置 *DirectBuffer*、*MapperedByteBuffer*（零拷贝）减少内存拷贝，让传输更高效
	- 与同步性能对比（100 并发以上）
		- 响应时间减少 4 倍
		- 吞吐量增加 3 倍
3. 选型
	- 传统 *Future*：无法方便获取任务**结果**和**异常处理**
	- *ListenableFuture*：容易陷入**回调地狱**影响代码阅读性
	- *CompletableFuture*：不太适合处理**复杂**任务
	- *RxJava*：在安卓中比较通用，与 *Spring* 融合度不高
	- [[src/java/Spring Reactor#Reactor\|Spring Reactor]]

## 框架组件

1. [[src/java/Java IO#BIO、NIO、AIO 有什么区别？\|NIO 请求框架]]
	- 可以充分利用 CPU 的资源，增大吞吐量
	- *Spring DeferredResult*：异步行为对客户端透明
	- *AsyncHttpClient*：NIO HTTP 请求
	- *Async Redis*： NIO Redis 请求
2. 效果
	- 12G、8 核的 Pod 估计能抗 1K 多 的 QPS

## 具体实现

![](/img/user/attachments/images/agency-system-全流程异步化-1.png)


1.  由 Tomcat NIO 线程来执行 Controller 内的逻辑，在此期间内发起一次异步的 Redis 调用，获取缓存报价，执行完 Controller 逻辑后**该线程暂时被回收**，处理其他的客户端请求。
2.  当 Redis 获取到报价数据后通过回调**并切换线程到自定义处理报价的线程**进行新鲜度的判断，如果缓存为空或者过期则会通过 AsyncHttpClient 向代理商发起一次异步 HTTP 请求获取实时报价。
3.  等到代理商接口返回后，**回调到原先处理报价的线程池**，来进行处理和加工报价。最终由 Tomcat NIO 线程将数据写入到输出流，返回给客户端。
4.  异步地将报价写入到缓存当中。

## 并行操作

1. 获取到每个代理商的变价事件（酒店 ID、fromDate、toDate），对每个事件进行并行处理（`flatMap`）
	- `parallel`：并行个数
	- `runOn`：指定线程池上执行
2. merge 所有并行的结果（`collectList`），统计成功数量和错误数量

