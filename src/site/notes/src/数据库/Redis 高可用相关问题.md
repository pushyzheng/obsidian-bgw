---
{"dg-publish":true,"dg-permalink":"Redis 高可用相关问题","permalink":"/Redis 高可用相关问题/"}
---


#数据库 #Redis 

## Redis 主从同步是怎么实现的?

Redis 的主从是通过 [Replication（复制）](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20%E5%A4%8D%E5%88%B6)来实现。

1. **全量复制**：Slave 在**第一次同步**时使用的复制模式
	- Master 通过 BGSAVE 生成 RDB 文件
	- 通过网络传输，发送给 Slave
	- Slave 接收到后使用 RDB 文件恢复数据
1. **长连接命令传播**：
2. **增量复制**：从 Redis 2.8 开始，断线重连后采取[增量的恢复方式](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20%E6%96%B0%E7%89%88%E5%A4%8D%E5%88%B6)（之前用的是全量复制）

## Redis 是怎么优化断线重连同步的？

1. Redis **2.8** 以前：*SYNC* 为全量同步，对于断线重连的情况效果不好
	- RDB 文件大，网络传输时间长
	- 需要全量恢复 RDB，耗时长
2. 2.8+ 后：使用 *PSYNC* 提供两种[新的复制方式](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20%E6%96%B0%E7%89%88%E5%A4%8D%E5%88%B6)：
	- 全量重同步：和 *SYNC* 类似
	- 部分重同步：只同步**断线期间**丢失的数据
		- 原理是通过**缓冲区**和**缓冲偏移量**来实现的：

![Pasted image 20220326180154.png|500](/img/user/attachments/images/Pasted%20image%2020220326180154.png)

## Redis 如何保证高可用？

Redis 的三种高可用架构如下所示：


| 架构          | 专注                                   | 缺点                                |
| ------------- | -------------------------------------- | ----------------------------------- |
| 主从架构      | 避免**单点故障**，进行读写分离         | Master 宕机无法恢复                 |
| Sentinel 哨兵 | 自动故障转移达到高可用                 | 受限于单机内存和 Master 写能力      |
| Cluster 集群  | 解决**单机内存瓶颈**，提高内存的利用率，支持动态扩容 | 不支持 Pipeline、缺失事务、原子功能 |

[Sentinel](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20Sentinel) 解决了传统主从架构 Master 宕机后，需要手动切换的难题。具有**集群监控**、[自动故障转移](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20Sentinel%20%E7%9A%84%E6%95%85%E9%9A%9C%E8%BD%AC%E7%A7%BB%E6%B5%81%E7%A8%8B)、配置中心、消息通知等功能。

但是 Sentinel 会引起**资源浪费**，内存可用性低：因为每台机器上的数据是一样的。

## Sentinel 的故障转移过程？

1.  **主观**状态检测：当前 Sentinel 结点检测到某个结点为未返回 *PONG*
2.  **客观**检查确定是否真正下线：询问其他 Sentinel 结点，是否**大于等于 2 个**
3.  执行故障转移：选举出一个**领头 Sentinel**，由其来完成故障转移

## Cluster 解决了什么问题？

[Cluster](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20Cluster%20%E9%9B%86%E7%BE%A4) 实现了分布式存储，解决主节点的写能力、容量受限于单机配置的问题，难以动态扩容的问题。

1. 最小的配置，3 主 3 从
2. [[src/分布式/分布式哈希\|分布式哈希]]方案使用的是带有虚拟节点的一致性哈希分区
	- 将所有的键哈希到 0～16383 个整数槽内
	- 每个结点都负责**一部分**的槽

![redis-Redis - Cluster.png|400](/img/user/attachments/images/redis-Redis%20-%20Cluster.png)


但是，Cluster 只能多 Key 在同一个结点下的事务功能，无法保证多结点下的事务。但可以通过 [Hash Tag](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20Hash%20Tag) 将相关的 Key 路由到同一个结点下来解决。