---
{"dg-publish":true,"dg-permalink":"MySQL 索引","permalink":"/MySQL 索引/"}
---


#数据库 #MySQL 

## B-树和 B+ 树的数据结构有什么区别？

1. B 树
	- 平衡的 N 叉树，通过每个结点存储更多数据，相对于二叉树**更加扁平**（树高更低）
	- **每层结点**都会存储数据
2. B+ 树
	- 是 B-树的基础上的变形，更加**扁平**
	- 每层的元素**不保存数据**，只用来索引（指向下一层的指针）
	- 叶子结点存储真实的数据，并以**双端链表**相连接，便于前后查找、范围查找、排序

## MySQL 索引底层结构为什么使用 B+树?

详细参考：[为什么 InnoDB 要选择使用 B+ 树作为索引的数据结构？](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Fquestions%2F%E4%B8%BA%E4%BB%80%E4%B9%88%20InnoDB%20%E8%A6%81%E9%80%89%E6%8B%A9%E4%BD%BF%E7%94%A8%20B%2B%20%E6%A0%91%E4%BD%9C%E4%B8%BA%E7%B4%A2%E5%BC%95%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%9F)

三个方向：
1. 为什么不使用常见的哈希表、有序列表呢？
	- 哈希表：MySQL 存在**复杂的查询**和**排序**操作
	- 有序列表：更新效率低
2. 为什么不使用二叉树？
	- 二叉树的**高度不平衡**，增大硬盘的随机 I/O 次数
3. 为什么不使用 B-树呢？
	- 会有更多磁盘 I/O
	- 查询**性能不稳定**
	- 范围查找效率低

## MySQL 中都有哪些索引？

1. **物理**维度划分
	- [聚簇索引](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95)（一级索引）：B+ 树的叶结点存储的**真实行数据**，InnoDB 中有且只有一个
	- [非聚簇索引](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E9%9D%9E%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95)（一级索引）：B+ 树叶节点存储的是**主键 ID**，如果需要查询完整数据，需要进行回表
2. **字段特性**划分
	- 主键索引：以主键作为索引
	- 唯一索引：相同列的数据保证唯一
	- 普通索引：普通字段
	- [前缀索引](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E5%89%8D%E7%BC%80%E7%B4%A2%E5%BC%95)：以字符串的**前几个字节**作为索引
3. 索引列的**个数**
	- 单列索引：单列
	- 联合索引：多列

## 索引设计的原则

1. 原则**区分度高**的字段作为索引，降低重复率
2. 尽量使用**短索引**，减少索引的空间占用，长字段可以使用前缀索引
3. 利用[[src/数据库/最左匹配原则\|最左匹配原则]]

## 索引失效的情况有哪些?

1. 不遵守[[src/数据库/最左匹配原则\|最左匹配原则]]的查询
2. 查询列是 varchar，但查询语句中使用 long 类型，导致出现**类型隐式转换**
	- 隐式类型转换还会出现查询结果不准确的问题
3. 对索引列**进行运算**或使用函数，如 `... WHERE id + 1 = 2;`
4. 使用 **is not null** 查询，如：`... WHERE id IS NOT NULL;`
5. 使用 **NOT IN** 查询

## MySQL 的慢查询优化思路？

1. 开启并收集**慢查询日志**，定位慢查询 SQL
2. 用 *explain* 语句分析 SQL，特别关注两个字段
	- type: 避免出现 **ALL**（全表扫描）
	- extra：避免出现 Using temporary（使用临时表，**group by** 没有索引）和 Using filesort（使用文件排序，**order by** 没有索引）
3. 优化 SQL 语句
	- 避免复杂的子查询，用 join 来代替
	- 合理设计索引：[[src/数据库/最左匹配原则\|最左匹配原则]]

## 大数据量级下使用 LIMIT N,M 会出现什么问题？

会出现**深度翻页**问题：
1. 问题
	- 因为 `LIMIT N, M` 相当于 `LIMIT M OFFSET N`
	- 会先扫描 M + N 行，再丢弃前 N 行，取最后的 M 行，**效率低下**
2. 解决办法
	- 使用自记录的**偏移量**（ID）来走索引查询
	- 例如：`WHERE id > 3000000 LIMIT 100`