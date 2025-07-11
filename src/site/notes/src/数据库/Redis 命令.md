---
{"dg-publish":true,"dg-permalink":"Redis 命令","permalink":"/Redis 命令/"}
---


#数据库 #Redis 

## zset 相关的命令都有哪些？

1. ZADD：添加元素
2. ZRANK：获取排名
3. ZRANGEBYSCORE：通过 score 进行范围查找
4. ZREMRANGEBYRANK：通过排名区间进行删除
5. ZSCORE：获取分数
6. ZSCAN：扫描有序集合

## pipeline 功能是什么？有什么好处吗？

1. 功能
	- 能将一组 Redis 命令**进行组装**，通过一次传输给 Redis 并返回结果集
	- 原理
		- 客户端组装打包，flush 后再统一请求
		- 服务端进行排队执行
2. 优点
	- 提高**吞吐量**
	- 降低网络 I/O 的**资源消耗**
3. 缺点
	- 不能保证原子：服务端是排队等待执行
	- 不是事务的：执行过程中异常，之前的命令**不会回滚**

## Redis 的 Lua 脚本

1. 概念
	- 实现组合命令的原子操作
2. 优点
	- **原子性**：Redis 会原子执行 lua 脚本，期间不会有其他命令被执行
	- 减少网络开销：多条命令一次性打包并返回