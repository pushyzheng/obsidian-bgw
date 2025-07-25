---
{"dg-publish":true,"dg-permalink":"redo log","permalink":"/redo log/"}
---


#数据库 #MySQL 

redo log是InnoDB存储引擎的**物理**日志，实现了事务中的持久性。

## 为什么需要 redo log?

1. 由于[[src/数据库/Buffer Pool\|Buffer Pool]]是基于**内存**的，不可靠，如果出现断电，脏页的数据将不会落盘
2. redo log 就是将本次对**这个页的修改**以日志的形式记录下来

## 什么是两阶段提交

1. ==prepare 阶段==
	- 事务操作时 redo log 先落盘，仅标记为**准备提交**，事务尚未最终提交
	- redo log 已持久化，但 binlog 还未写入
2. ==commit 阶段==
	- prepare 阶段成功后，再写入 [[src/数据库/binlog\|binlog]] —— 代表逻辑上的事务提交
	- binlog 写入成功后，redo log 再写入 commit 标识，标记事务为真正提交
	- 该阶段完成后，事务才算**正式提交**

## 为什么需要两阶段提交？

如果不使用两阶段提交，会导致**崩溃之后主从复制**出现问题：
1. 先写redo log再写binlog崩溃
	- 恢复时 redo log 认为事务已经完成
	- 但 binlog 没写，导致**主从复制**的会丢失事务数据
2. 先写binlog再写redo log崩溃
	- 恢复时redo log没有该事务数据，**事务回滚**
	- 但 binlog 已有该事务修改的数据，导致主从数据不一致

## redo log 和 undo log 区别在哪？

都属于 InnoDB 存储引擎的日志，区别在于：
1. redo log 记录事务**修改后**的数据状态
	- 记录更新**之后**的值
	- 用于事务崩溃恢复、保证事务的**持久性**
2. undo log 记录事务**修改前**的数据状态
	- 记录更新**之前**的值
	- 用于事务的回滚、保证事务的**原子性**

## redo log 什么时候会刷盘？

- MySQL **正常关闭**
- redo log buffer 写入量大于 redo log buffer 内存空间的**一半**
- InnoDB 的后台线程**每隔一秒**，会将 redo log buffer 持久化磁盘

## redo log 和 binlog 有什么区别？

[[src/数据库/binlog#redo log 和 binlog 有什么区别？\|binlog#redo log 和 binlog 有什么区别？]]

## 主从复制或者备份恢复可以使用 redo log 吗？

不可以：redo log 文件是**循环写**，是会边写边擦除日志的。

只记录未被刷入磁盘的数据的物理日志，已经刷入磁盘的数据都会从 redo log 文件里**擦除**