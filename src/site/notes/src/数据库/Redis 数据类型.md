---
{"dg-publish":true,"dg-permalink":"Redis 数据类型","permalink":"/Redis 数据类型/"}
---


#数据库 #Redis 

## SDS

- 数据结构：字节数组，并记录长度 len、alloc（申请空间大小）
- 优点：
	- 防止溢出：计算 *remain = alloc - len*，避免溢出

## List

- [普通链表](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20List%20%E9%93%BE%E8%A1%A8)
	- 数据结构：双向链表，存储 len
- [压缩列表](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20ziplist%20%E5%8E%8B%E7%BC%A9%E5%88%97%E8%A1%A8)（Redis 3.2 之前数据量小使用）
	- 数据结构：**连续内存**的数据块，类似于数组
	- 优点
		- 连续内存，很好利用 CPU 缓存
		- 采用不同的编码方式保存不同大小的数据，节省内存开销
	- 缺点
		- 查询效率低：除头尾元素外，查询效率为 O(N)
		- 连锁更新：指定位置插入元素时，由于记录前一项长度，可能会**触发连锁更新**
- [quicklist](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20quicklist)（Redis 3.2+ 开始使用）
	- 数据结构：双向链表 + 压缩列表，减少压缩列表的长度，降低连锁更新发生
	- 优点：规避连锁更新的风险，提高性能
	- 缺点：并**没有完全解决**连锁更新的问题
- [listpack](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20listpack)（Redis 5.0+ 开始使用）
	- 数据结构：和压缩列表类似，依然是连续内存的数据块
	- 优点
		- 彻底解决连锁更新的问题：不记录前一项长度，**只记录当前项长度**

## HashTable

数据结构：两个数组，用来做渐进式 rehash

## ZSet

数据结构：哈希表 + [跳表](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FRedis%20skiplist%20%E8%B7%B3%E8%A1%A8)

## 跳表

- 由链表基础上改进的，是一种多层的有序链表
- 数据结构
	- 每个结点都是多层，每层都是一个索引
- 查找过程
	- 从头结点最高层开始
	- 如果当前 score < 查找 score（如果相等会比较 SDS 大小）访问该层下一个结点
	- 否则查找下一层
- 计算排位（rank）
	- 计算从头结点开始到目标结点，沿途所有的跨度（同层中两个结点的间距）之和
- 时间复杂度为 **O(logN)**