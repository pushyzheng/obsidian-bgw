---
{"dg-publish":true,"dg-permalink":"Buffer Pool","permalink":"/Buffer Pool/"}
---


Innodb 存储引擎设计了一个缓冲池（Buffer Pool），来提高数据库的**读写性能**。

![Pasted image 20250724112525.png](/img/user/attachments/images/Pasted%20image%2020250724112525.png)

## Buffer Pool 的工作机制？

如果有 Buffer Pool 的话：
- ==读取数据==
	- 如果数据存在缓存中，客户端会直接读取缓存中的数据
	- 否则再去磁盘读取
- ==修改数据==
	- 如果数据存在缓存中，直接修改缓存中数据**所在的页**
	- 将其设置为**脏页**（表示内存数据和磁盘数据出现不一致）
	- 为了减少磁盘 I/O(**WAL技术**)，不会立即将脏页写入磁盘，而是由后台在**合适时机**将脏页写入磁盘

## Buffer Pool 缓存什么？

Buffer Pool 同样是按照[[src/数据库/MySQL 存储结构#表空间文件的结构是怎么样的?\|页]]来划分的：
1. MySQL 启动时，InnoDB 会申请一片**连续的**内存空间
2. 划分出一个个页，称为**缓存页**