---
{"dg-publish":true,"dg-permalink":"HashMap","permalink":"/HashMap/"}
---


#Java #Java基础 

## HashMap 和 HashTable 的区别？

1. 空值不同
	- HashMap 允许有空的 Key 和 Value
	- HashTable 则不允许
2. 线程安全
3. 效率不同
4. 扩容不同
	- HashTable 的初始长度是11，每次扩容为 2n + 1
	- HashMap 初始长度是 16，每次扩容为 2n

## HashMap 的实现原理？

1. JDK 1.7
	- 数据结构：数组 + 链表实现
	- **头插法**：出现哈希冲突时，插入到链表的头部，[有死循环的风险](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Fquestions%2FHashMap%201.7%20rehash%20%E5%A4%B4%E6%8F%92%E6%B3%95%E6%88%90%E7%8E%AF%E9%97%AE%E9%A2%98%3F)
2. JDK 1.8
	- 数据结构：数组 + 链表 or **红黑树**实现
		- 
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/数据结构 - 红黑树/#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



## 特点

1. 自平衡的二叉查找树：在插入和删除时，会保持二叉查找树的平衡
2. 查找的时间复杂度为 O(logN)
3. 从根到叶子的**最长**的可能路径，不多于**最短**的可能路径的**两倍长**


</div></div>

	- **尾插法**
	- **树化**
		- 当链表长度大于 **8** 时
			- 如果当前容量小于 64：直接扩容
			- 大于 64：转换为红黑树，提高查找的速度
		- 小于等于 **6** 时，又退化成链表

## 为什么 HashMap 要树化？

1. 一方面是**性能**问题
	- 哈希冲突过多的话，O(1) 的查询效率就会退化为 **O(N)/O(logN) 的链式查找**，性能较低
2. 但本质上**安全**问题
	- 避免造成[哈希碰撞拒绝服务攻击](https://www.ruanyifeng.com/blog/2018/09/hash-collision-and-birthday-attack.html)。

## HashMap 可不可以不使用链表呢？

主要是时间和空间上折中的考虑，哈希冲突比较少时（链表长度不长）时
- 量级小的情况下，红黑树的 O(logN) 的与链表的 O(N) 对比优势不明显
- 在更新时，需要额外时间来维护红黑树的平衡

## 为什么 HashMap 的容量要是 2 的倍数？

参考：[为什么 HashMap 的容量要是 2 的倍数？](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Fquestions%2F%E4%B8%BA%E4%BB%80%E4%B9%88%20HashMap%20%E7%9A%84%E5%AE%B9%E9%87%8F%E8%A6%81%E6%98%AF%202%20%E7%9A%84%E5%80%8D%E6%95%B0%EF%BC%9F)

1. 2 的倍数减一的数，都有一个特点：**二进制所有位都是 1**；
2. 这样任意一个数和 n -1 做「与」运算，**都能保证在 [0, n - 1] 内**。

## Java HashMap 如何减少哈希冲突的？

参考：[Java HashMap 如何减少哈希冲突的？](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20HashMap%20%E5%A6%82%E4%BD%95%E5%87%8F%E5%B0%91%E5%93%88%E5%B8%8C%E5%86%B2%E7%AA%81%E7%9A%84%EF%BC%9F)

通过扰动函数，让**高位的信息变相保留下来**，参与哈希槽下标的计算。

## HashMap 的扩容过程？

1. 为什么要扩容？
	- 没有一种哈希方法能保证绝对的哈希**均匀**
	- 扩容是为了让哈希表分散的**更均匀**
2. 什么时候扩容？
	- 初始化**懒加载**：在第一次 put 的时候
	- `size > threshold (capacity * loadFactor)` 时
3. 扩容的过程？
	- 普通扩容：将容量、阈值和负载因子**都扩大到 2 倍**；
	- 初始化：
		- 空的构造函数：设置容量为 *16*；
		- 有参构造函数（**指定容量**）：设置容量为**不小于**指定容量的 **2 的幂数**赋值给阈值
	- [[src/java/HashMap#rehash 的过程\|rehash]]

## 说一下 HashMap 的 put 过程？

1. [[src/java/HashMap#JDK 1 8 HashMap 的懒加载过程？\|懒加载]]
2. 计算哈希值和索引下标
3. 判断是否有哈希冲突
	- 没有：直接插入
	- 有：追加到末尾，如果链表的长度大于 8：
		- 如果容量小于 64：resize 扩容
		- 大于 64：则进行树化
4. 如果容量大于阈值（容量 $\times$ 负载因子），则：
	- resize 扩容
		- 容量和阈值都扩大为原本的两倍，最大值为 *Integer.MAX_VALUE*
	- [[src/java/HashMap#rehash 的过程\|rehash]]

## JDK 1.8 HashMap 的懒加载过程？

1. 无参
	- 容量设置为 16
2. 有参（指定容量）
	- 初始：容量设置为**不小于**指定容量的 2 的**幂数**
	- 懒加载
		- 将该值赋值给阈值
		- 将阈值赋值给容量，然后让阈值 = 容量 $\times$ 负载因子

## rehash 的过程

1. 因为底层数组的**长度**发生变化，需要重新分配空间，将元素重新插入到新的空间中
2. 1.8 优化
	- 采用**尾插法**，防止出现死循环
	- 不需要重新计算所有 Key 的哈希值，而是做与运算结果是否**等于 0** 来判断：
		- *newIndex = oldIndex*
		- *newIndex = oldIndex + oldSize*