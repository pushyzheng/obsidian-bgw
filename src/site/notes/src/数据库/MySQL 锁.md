---
{"dg-publish":true,"dg-permalink":"MySQL 锁","permalink":"/MySQL 锁/"}
---


#数据库 #MySQL 

## MySQL 中有哪些锁？

1. 全局锁
	- **全库**变为 *readonly* 状态
	- 用途：全局逻辑备份
2. 表级锁
	- 表锁
		- 行锁未出现之前解决并发问题
	- 意向锁
		- 提高判断**是否有行记录被加锁**的效率
		- 使得行锁和表锁共存
	- MDL 锁：
		- 防止 DML 和 DDL 的冲突
			- 执行 DDL （结构变更）时，加写锁
			- 执行 DML （CRUD）时，加读锁
3. 行级锁
	- 记录锁（Record lock）
		- 降低**并发控制的粒度**
	- 间隙锁（Gap lock）
		- 锁住一个**范围**
	- Next Key 锁
		- Record lock + Gap lock
		- 用于[避免出现幻读问题](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FMySQL%20%E6%98%AF%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3%E5%B9%BB%E8%AF%BB%E7%9A%84%EF%BC%9F)

## MySQL 意向锁解决了什么问题？

[MySQL 意向锁解决了什么问题？](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Fquestions%2FMySQL%20%E6%84%8F%E5%90%91%E9%94%81%E8%A7%A3%E5%86%B3%E4%BA%86%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98%EF%BC%9F)

1. 提高了是否可以加锁的**判断效率**
2. 实现了行锁和表锁的多粒度锁机制，使得表锁和行锁**可以共存**

## MySQL 是如何解决幻读的？

1. 对于**快照读**：MVVC 机制本身就可以实现，即不会出现幻读的
2. 对于**当前读**：通过 *Next key lock（Record lock + Gap lock）*来实现的
	- 假设对 a = 8 执行当前读，会加以下的锁：
		1. 记录锁：锁住 a = 8 行的本身
		2. 间隙锁：(5, 8) 区间所有的行
		3. Next Key（间隙锁）：(8, 11] 范围
3. MySQL **并没有完全解决**幻读，如：
	- 事务 A 先执行 `id > 100` 的快照读
	- 事务 B 插入 `id = 200` 的记录
	- 事务 A 执行 `id > 100 for update` 的当前读，与之前读取到的数据不一致

## 使用 MySQL 如何避免死锁？

1. 大事务化成小事务
2. 避免加表锁：尽量使用索引访问数据，这样可以加行锁