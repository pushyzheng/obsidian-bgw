---
{"dg-publish":true,"dg-permalink":"LiveIn","permalink":"/LiveIn/"}
---


#项目 

- 为了提高公域的内容质量，添加自营账号的管理：
	- 自营账号发布 Moment 不经过审核
	- 给自营账号加权逻辑：Feed 流中每 2 个 Moment 就插入一个自营账号的内容
		- 去重，使用 Redis 的 ZSET 来完成去重

## 去重实现

参考：[Redis ZSet 实现最新浏览记录](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20ZSet%20%E5%AE%9E%E7%8E%B0%E6%9C%80%E6%96%B0%E6%B5%8F%E8%A7%88%E8%AE%B0%E5%BD%95)

1. 数据结构
	- key 后缀拼接用户 ID
	- member 存储浏览记录的 ID
	- score 存储时间戳
2. 流程
	- 记录：*ZADD* 记录 ID 和当前时间戳
	- 清除 [[src/数据库/Redis 命令#zset 相关的命令都有哪些？\|ZREMRANGEBYRANK]] 根据排名，删除倒数 **[0, -1001]** 条数据，即保留最新的 1000 条数据
	- 判断是否浏览：*ZRANK*  返回的排名是否为 nil
3. 清除的时机选择
	- **定时**清除：需要额外来记录所有的用户 ID
	- 每次记录时清除
4. 为什么要选择使用 zset 呢？
	- 可以基于**现有的**存储系统 Redis 来实现，避免引入其他的存储依赖
	- **工期和人力成本**的考量