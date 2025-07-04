---
{"dg-publish":true,"dg-permalink":"MySQL 事务","permalink":"/MySQL 事务/"}
---


#数据库 #MySQL 

## 事务的概念是什么？

1. 四大特性：
	- 原子性
	- 一致性
	- 隔离性
	- 持久性
2. 可能导致的错误：
	- 脏读：读取到其他事务还**未提交的修改**数据
	- 不可重复读：一个事务中两次查询同一记录，返回**不同**数据
	- 幻读
		- 事务 A 读取某个**范围**记录时，之后事务 B 在该范围修改
		- 事务 A 再次读取该范围时，出现幻行

## 请你谈谈 MySQL 事务隔离级别，MySQL 的默认隔离级别是什么?

1. 四种隔离级别：
	- **读未提交**
		- 一个事务可以读取到另一个事务尚未提交的数据
		- 会出现脏读、幻读的情况
	- **读已提交**（==Oracle、SQL Server== 默认的级别）
		- 允许读取其他事务已经提交的数据
		- 避免了脏读，但仍可能出现不可重复读和幻读
	- **可重复读**（==InnnoDB== 引擎默认的级别）
		- 保证同一事务中**多次**读取相同数据结果**一致**
		- 避免脏读和不可重复读，但幻读仍可能发生
	- **串行化**
		- 事务按顺序执行
		- 完全避免脏读、不可重复读和幻读
2. 实现原理
	- 未提交读：没有任何限制
	- 已提交读：通过 [[src/数据库/MySQL 锁#MVVC 多版本并发控制机制\|MVVC 多并发版本控制]]来实现，每次[[src/数据库/MySQL 锁#什么是快照读和当前读？\|当前读]]时都生成一个读快照
	- 可重复读
		- 事务**开始到结束**都使用同一个视图
		- [[src/数据库/MySQL 锁#Next Key 锁\|Next Key 锁（行锁 + 间隙锁）]]
	- 串行化：加锁单线程执行

## 事务的实现原理？

事务是基于 redo log 、 undo log 以及 MVCC（保证隔离性）实现的：
1. 原子性、持久性
	- redo log  的**两阶段提交**
2. 一致性
	- undo log 实现回滚
3. 隔离性
	- MVCC 机制实现读已提交、可重复读隔离级别
	
## 长事务会有那些问题？

1. 回滚段不会被清除
	- 由于 MVCC 的机制，长事务会保留**很老的**事务视图
	- 会占用大量的**存储空间**
2. 占用锁资源
	- 长事务会一直占用**锁资源**，从而影响系统性能

## 参考资料

> [!QUOTE] 
> [面试八股文——MySQL篇](https://zhuanlan.zhihu.com/p/422357431)