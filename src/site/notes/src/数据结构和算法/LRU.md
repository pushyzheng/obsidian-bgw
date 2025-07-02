---
{"dg-publish":true,"dg-permalink":"LRU","permalink":"/LRU/"}
---


#算法 

## 概念

LRU （*Least Recently Used*），即最近最少使用的算法。

是一种常用的页面置换算法，当元素到达最大缓存容量时，将会选择**最近最久未使用**的页面予以淘汰。

## 实现

### 哈希表 + 双向链表

该算法可以使用哈希表，辅以双向链表记录键值对的信息来实现

| 数据结构 | 作用                                               |
| -------- | -------------------------------------------------- |
| 哈希表   | 用来缓存数据的键，映射到双向链表的位置             |
| 双向链表 | 按被**使用的顺序**来存储数据，越靠近**尾部**的，使用的越少 |


![](/img/user/attachments/images/LRU.png)

具体实现为：

1. get
	- 从哈希表中以 O(1) 找到结点，如果为空直接返回
	- 如果不为空，将结点**移动到链表头部**（代表最近使用过）
2. put：
	- 从哈希表中获取结点：
		- 如果不为空，覆盖结点值，并**移动到链表头部**；
		- 如果为空：
			- 创建新的节点，并放置到链表**头部**
			- 判断容量是否到达缓存最大容量时，如果是则**删除尾结点**。

在双向链表实现中，这里使用一个**伪头部**和**伪尾部**标记界限，这样在更新的时候就不需要检查是否是 null 节点。

参考代码：[HashMapAndLinkedList.java](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/algorithm/lru/impl/HashMapAndLinkedList.java)

### 基于 JDK 的 LinkedHashMap

参考代码：[ImplementationByLinkedHashMap.java](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/algorithm/lru/ImplementationByLinkedHashMap.java)


## 例题

https://leetcode-cn.com/problems/lru-cache/solution/