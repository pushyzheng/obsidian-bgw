---
{"dg-publish":true,"dg-permalink":"Redis 基础","permalink":"/Redis 基础/"}
---


#数据库 #Redis 

## Redis 的优缺点各是什么？

1. 优点
	- 性能高：纯内存操作，单机并发高
	- 丰富的数据结构：List、HashMap、ZSet 等
	- 原子性：所有操作都是原子的，可以用来实现分布式锁、分布式限流器等
	- 丰富的特性：Pub/Sub 机制、Key 过期机制等
2. 缺点
	- 受限物理内存：无法在**海量**数据上，进行高性能读写
	- 不具备自动的容错和恢复：容易丢失数据
	- 很难支持在线扩容

##  Redis 有哪些应用场景？

1. 热点数据缓存
2. Session  共享
3. 简单消息队列（List 的结构来实现）
4. **排行榜**（Sorted Set 排序集合来实现）
5. 限流器（incr 原子机制）

## Redis 为什么这么快？

-   纯内存操作：读写数据的时候都不会受到硬盘 I/O 速度的限制
-   单线程操作：避免了不必要的上下文切换和竞争条件
-   使用多路 I/O 复用模型，非阻塞 IO
-   简单并高效的数据结构

## Redis 持久化 RDB 和 AOF 优缺点

1. [RDB - Redis Data Base](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRDB)
	- 快照形式，是默认的方式
	- 优点：
		- **数据安全性低**：每次间隔一段时间执行，有*丢失数据*的可能
		- **容灾性好**：存储为*单独*文件，方便持久化，适合做冷备，可手动执行备份（SAVE/BGSAVE）
		- **启动效率高**：直接加载到内存中即可
	- 缺点
		- 故障后丢失的数据相对较多
2. [AOF - Append Only File](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FAOF)：
	- 命令追加形式
	- 优点
		- **数据安全性高**：每次执行命令都会追加到缓存区，且执行通过  *appendfsync* 来控制每次都执行刷盘，能保证数据的最小丢失
		- **写入性能高**：以 *append-only* 模式追加，无寻址开销
		- **对服务性能影响小**：fork 子进程来完成的
	- 缺点：
		- **启动效率低**（数据文件大）：AOF 文件 > RDB 文件大小，数据集大时启动恢复较慢（虽然有[[src/数据库/Redis 原理#rewrite 机制是如何实现的？\|rewrite 机制]]来减缓），不适合做冷备

## Redis 的瓶颈是什么？

1. CPU 不是 Redis 的瓶颈
	- 是基于内存操作的
2. 机器内存大小
	- Cluster 集群模式解决单机的内存限制的瓶颈
3. 网络带宽
	- 6.0+ 在网络 I/O 总引入了多线程

## Redis 是单线程架构嘛?

> [!INFO] 
> 考查：线程模型、异步机制（子进程+ 线程 Redis 6.0 多 I/O 线程）

1. [Redis 线程模型](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B)
	- 基于 [[src/计算机基础/IO#Reactor 有哪三种线程模型？\|Reactor 线程模型]]
	- 使用多路复用技术，来尽量减少网络 I/O 的时间消耗
2. Redis 的单线程是指？
	- 通常是指**处理命令**是使用的单线程，但还是会有其他异步线程的：
		- ***BGSAVE***：*Fork* 子线程来存储快照
		- ***unlink***：异步删除对象
3. Redis 6.0+ 的多线程？
	- 只是在**网络 I/O** 阶段中增加了多线程
	- 在执行命令的环节（**数据操作模块**）中，仍然是单线程（主线程）执行的

![redis - IO 多路复用.png](/img/user/attachments/images/redis%20-%20IO%20%E5%A4%9A%E8%B7%AF%E5%A4%8D%E7%94%A8.png)

> [!INFO] 为什么要引入网络 I/O 多线程？
> Redis 的性能瓶颈可能出现网络 IO 的读写，单线程处理网络读写的速度跟不上底层硬件的速度。

## Redis 为什么是单线程的？

1. 单线程是指「网络请求模块使用了一个线程」。因为：
	- Redis 是**纯内存**操作，CPU 不是 Redis 的瓶颈
	- Redis 的瓶颈是**机器内存和网络带宽**
2. 优缺点：
	- 优点：**开发简单**，避免线程的上下文切换和系统调用
	- 缺点：如果某个指令执行过长，或导致**整个服务不可用**，如：`key *`、删除一个大 key

## Redis 事务

1. 事务特性：
	- **原子性**：要么全部成功、要么全部失败；
	- **顺序性**：事务中的指令按序执行；
	- **排他性**：事务执行期间，其他命令将不会执行（单线程模型）
2. 相关命令：
	- [MULTI](http://redisdoc.com/transaction/multi.html#multi) 
	- [EXEC](http://redisdoc.com/transaction/exec.html#exec) 
	- [DISCARD](http://redisdoc.com/transaction/discard.html#discard)
	- [WATCH](http://redisdoc.com/transaction/watch.html#watch)

## Redis 的过期键的删除策略

1. [经典的两种缓存过期策略](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E7%BB%8F%E5%85%B8%E7%9A%84%E7%BC%93%E5%AD%98%E8%BF%87%E6%9C%9F%E7%AD%96%E7%95%A5)
	- 惰性（被动）：会导致**数据堆积**，对内存敏感的 Redis 不利
	- 定时（主动）：轮询**耗费资源**，会降低性能和吞吐
2. Redis 采取惰性 + 定时配合使用的策略
	- 定时（默认 100ms）
		1. 随机抽取 M 个 Key
		2. 删除其中的过期 Key，假设数量为 N
		3. 如果 N/M > 25%，继续重复操作 1，直到低于 25%
	- 惰性：在对某个 Key 操作时，检查一下是否过期

## Redis 内存淘汰策略

1. 概念：是 Redis 在内存达到**最大内存**时选择回收 Key 的[策略](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20%E7%9A%84%E8%BF%87%E6%9C%9F%E7%AD%96%E7%95%A5%E5%92%8C%E6%B7%98%E6%B1%B0%E7%AD%96%E7%95%A5)
2. 默认：不驱逐（noeviction），会导致**写操作失败**，但能保证数据的不丢失
3. 策略（相互组合而成）
	- 回收对象
		- volatile：设置过期时间的对象
		- allkey：所有对象
	- 回收算法
		- [[src/数据结构和算法/LRU\|LRU]]：最近最少使用（基于数据**最近访问时间**）
		- [[src/数据结构和算法/LFU\|LFU]]：最少使用（基于**访问次数**）
		- Random：随机
		- ttl：即将过期的 Key（基于 Key 的过期时间）

## Redis 大 Key 存在哪些问题？如何解决？

1. 存在的问题：
	- **主线程阻塞**：Redis 执行命令单线程特性，会导致后边请求被阻塞，严重时 Sentinel 会认为宕机进行故障转移
	- 数据倾斜：在 Cluster 环境下，会造成**节点内存不均匀**，严重导致大 Key 实例内存资源耗尽
2. 如何处理？
	- 查询：使用 [scan](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20Scan) 操作来扫描 key 或者哈希表中的 field；
	- 异步删除：使用 [unlink](https://www.redis.com.cn/commands/unlink.html) 来替换 del，先将键与键空间断开连接，再**异步地**执行实际的删除
	- 最好还是从业务上避免：不写大 Key，对其进行拆分

## Redis 热 Key 问题如何解决


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/分布式缓存/#key" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



## 如何处理缓存的热 Key？

1. 什么是热 Key？
	- 流量太集中，单个分片的结点资源达到上限，**负载不均匀**
	- 热 Key 请求同时落在一个 Redis 实例上，无法通过扩容解决
2. 如何发现热 Key？
	- 凭借业务经验预估（如某些城市的热 Key 概率高）
	- 在代码层、代理层做统计
3. 如何解决
	- 增加**本地缓存**
	- 读写分离，增加副本数量

参考：[Redis 热 Key 发现以及解决办法](https://dongzl.github.io/2021/01/14/03-Redis-Hot-Key/index.html)

</div></div>


## 参考资料

> [!QUOTE] 
> 
> [我没有三颗心脏 - Redis](https://www.wmyskxz.com/2020/03/25/ma-ma-zai-ye-bu-dan-xin-wo-mian-shi-bei-redis-wen-de-lian-du-lu-liao/)
> 
> [JavaKeeper - Redis](https://javakeeper.starfish.ink/interview/Redis-FAQ.html#%E4%B8%80%E3%80%81redis-%E5%9F%BA%E7%A1%80%E9%97%AE%E9%A2%98)
> 
> [Redis 面试那些事（30问与答）](https://jishuin.proginn.com/p/763bfbd69014)