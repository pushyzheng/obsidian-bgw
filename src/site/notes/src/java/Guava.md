---
{"dg-publish":true,"dg-permalink":"Guava","permalink":"/Guava/"}
---


#Java #Java框架 

## Guava 的 Cache 的底层实现？

Guava 和 *CurrentHashMap* 类似，但还具有其他特性：

| Name             | Desc                          |
| ---------------- | ----------------------------- |
| 最大元素个数限制 | 到达后会触发 **LRU** 驱逐     |
| 超时机制         | 过期将自动删除                |
| 弱引用和软引用   | 加速 GC 的收集                |
| 统计功能         | 可以统计访问的 QPS、miss 率等 |

底层的并发数据结构类似于 JDK 1.8 之前的 *CurrentHashMap* 分段锁设计。

![guava-cache.png](/img/user/attachments/images/guava-cache.png)

内部会有两个队列：
- *writeQueue*：写队列，按照写入时间排序的元素队列，新的元素会追加到**队尾**
- *accessQueue*：访问队列，访问时间排序的元素队列，最近访问过的会移动到**队尾**

通过这两个队列就可以实现**最近最少未使用**的算法，实现 *expireAfterWrite* 和 *expireAfterAccess* 两个过期功能。

## Guava 缓存的过期机制？

Guava 的缓存清除机制是一种惰性的**被动删除**的，懒删除机制。

即会在**写操作**的时候遍历 writeQueue 和 accessQueue，来检测出存在过期的元素（两个队列中的元素分别带有写入时间和访问时间的属性）

## Guava Cache 和 Ehcache 有什么区别？

Ehcache 框架支持多种存储方式：
1. 堆内缓存
2. 堆外缓存：拥有更大内存，不受 GC 管理
3. **本地磁盘**：持久化缓存，但需要序列化

而 Guava **只支持**堆内缓存。

另一方面 Encache 功能更加的全面，有内存大小统计、集群方案等。

相对来说，Guava Cache 更加的**轻量**。