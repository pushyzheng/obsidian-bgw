---
{"dg-publish":true,"dg-permalink":"Redis 应用","permalink":"/Redis 应用/"}
---


#数据库 #Redis 
 
## Redis 如何实现分布式锁？

[[src/分布式/分布式锁#Redis\|分布式锁#Redis]]

## Redis 如何实现消息队列？

1. [[src/数据库/Redis 数据类型\|List 实现]]
	- List 本身是一个双向链表，可以充当队列
	- LPUSH：生产消息；RPOP：消费消息
	- 缺点
		- 一条消息只能被一个消费者接收
		- 不支持分组消费
		- 不支持数据优先级（如果需要可以使用 zset）
2. 发布订阅模式
	- 通过 PUB/SUB 机制，实现多播
	- 缺点
		- 消息无法持久化，非常不可靠
3. Stream
	- Redis 5.0+ 新增的数据结构
	- 提供消息的持久化和主备复制功能