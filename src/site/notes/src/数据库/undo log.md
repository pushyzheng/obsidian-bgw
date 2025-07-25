---
{"dg-publish":true,"dg-permalink":"undo log","permalink":"/undo log/"}
---


#数据库 #MySQL 

undo log是InnoDB存储引擎用于**事务管理**的重要日志，实现了事务中的**原子性**。

## MySQL undo log 有什么用?

- ==事务回滚==
	- 记录事务修改数据之前的**旧版本**数据
	- 当事务需要回滚时，通过该日志恢复至修改前的状态，保证[[src/数据库/MySQL 事务#事务的概念是什么？\|原子性和数据的一致性]]
- ==[[src/数据库/MySQL 锁#MVVC 多版本并发控制机制\|MVCC实现]]==
	- 提供数据的历史版本，实现 MVCC 快照读中的【可重复读】与【读已提交】
	- 保证读到事务开始时的**旧版本数据**，避免读到未提交的数据

## undo log 的工作机制

1. 当执行 insert/update/delete 等修改操作时，会生成对应的**反向操作**的 undo log 记录
	- 如 delete 对应 insert，update 对应旧值保存
2. undolog **不能立即删除**
	- 需等待其他依赖旧数据版本的事务结束后，由purge线程**定期清理**

## redo log 和 undo log 区别在哪？


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/redo log/#redo-log-undo-log" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



## redo log 和 undo log 区别在哪？

都属于 InnoDB 存储引擎的日志，区别在于：
1. redo log 记录事务**修改后**的数据状态
	- 记录更新**之后**的值
	- 用于事务崩溃恢复、保证事务的**持久性**
2. undo log 记录事务**修改前**的数据状态
	- 记录更新**之前**的值
	- 用于事务的回滚、保证事务的**原子性**


</div></div>
