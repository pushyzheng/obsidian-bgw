---
{"dg-publish":true,"dg-permalink":"Redis 原理","permalink":"/Redis 原理/"}
---


#数据库 #Redis 

## rewrite 机制是如何实现的？

1. 目的：为了解决 AOF 随着命令执行过多，**文件过大**的问题
2. 原理
	- 会读取旧的 AOF 文件命令内容，根据不同的数据类型采取不同的逻辑
	- 将多个命令 Merge 为一个命令后，重新写入到新的 AOF 文件当中