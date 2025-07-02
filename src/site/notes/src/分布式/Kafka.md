---
{"dg-publish":true,"dg-permalink":"Kafka","permalink":"/Kafka/"}
---


#消息队列 #中间件 

## 为什么 Kafka 这么快？

[Kafka 的高性能实现](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Ftech%2FKafka%20-%20%E9%AB%98%E6%80%A7%E8%83%BD%E5%AE%9E%E7%8E%B0)有四个方面：

1. 批量读写
	- 数据以一批为单位进行传输和消费
2. 顺序读写
	- 减少磁盘寻址时间，提高 I/O 效率
3. [页缓存](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Fquestions%2F%E4%BB%80%E4%B9%88%E6%98%AF%E9%A1%B5%E7%BC%93%E5%AD%98%EF%BC%9F)
	- 减少 I/O 的次数
	- 提前将写操作排序好，减少磁头移动时间
4. [[src/计算机基础/操作系统#零拷贝的原理？\|零拷贝]]
	- 减少内核态到用户态的**重复传输**

## Kafka 的底层物理存储是怎样的？

1. 一个 Topic 以 *partition* 为单位进行物理分组，每个 partition 都是一个有序队列
2. 一个 *partition* 从物理上会由多个 *Segment* 组成：
	- `.idx`：索引文件
	- `.log`：数据文件

特性：
1. 对数据文件会顺序读写，提高 I/O 速度
2. 以文件名作为索引：
	- 每个 partition 是一个文件夹，后缀是 partition 的序号
	- Segment 中 `.idx` 和 `.log` 文件的后缀都是上一个 segment 文件最后一条消息的 offset 值

参考：[Kafka文件存储机制那些事](https://tech.meituan.com/2015/01/13/kafka-fs-design-theory.html)

## 如何保证 Kafka 的可靠性？

1. Producer
	- 配置 acks 参数为大于 1（同步给多个 partition）
	- 将异步发送修改为同步，以及重试
2. Broker
	- 配置 *Replication-Factor* 大于 1
	- 配置 PageCache 刷盘参数，尽可能地执行刷盘
3. Consumer
	- 避免异步处理业务逻辑
	- 关闭 auto commit，进行手动提交

## 消息堆积怎么办？

1. 排查思路
	- Producer 流量突增，发送消息过多
	- Consumer 消费异常，消费速率下降
2. 流量突增
	- 消息**不重要**：直接在 Consumer 端**丢弃**或异步进行消费
	- 消息**重要**的：如果此时消费者数量 < 分区数量：增加消费者数量至分区数量
3. 消费异常
	- 当消费速度过慢或者消费线程阻塞造成的
		- 查看耗时的**监控和日志**，查看**堆栈信息**进行排查

## 消息重试

Kafka 本身并没有重试机制和死信队列，需要自己实现：
1. 重试机制：使用 [Spring Kafka ](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FSpring%20Kafka)
	- 重试策略（原地重试、指数退避等）
	- 恢复器（忽略、转发 DLT 等）
2. 死信队列：可参考 [kafka消息重试](https://blog.csdn.net/sz85850597/article/details/86749783)

## 分区数越多，吞吐量就会越高吗？

https://www.modb.pro/db/429377

https://ost.51cto.com/posts/11319

## Kafka 为什么不需要读写分离？

1. **延迟**问题： 从 Leader 同步到 Followers 存在一定的延迟（刷盘、网络等）
2. Partition 本身的**负载均衡**：Kafka 通过分区的形式，天然均衡到不同的 Broker 中

## Kafka 中分区与中消费者和消费组的关系

同一个消费组内只会**部分**主题的数据（即一个应用里的机器）

不同消费组才能读取主题的**所有**消息（即不同的应用）

[Kafka 中分区与中消费者和消费组的关系](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FKafka%20%E4%B8%AD%E5%88%86%E5%8C%BA%E4%B8%8E%E4%B8%AD%E6%B6%88%E8%B4%B9%E8%80%85%E5%92%8C%E6%B6%88%E8%B4%B9%E7%BB%84%E7%9A%84%E5%85%B3%E7%B3%BB)

