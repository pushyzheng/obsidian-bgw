---
{"dg-publish":true,"dg-permalink":"Java 集合","permalink":"/Java 集合/"}
---


## Java 里面 List 的几种实现

- Vector
	- 早期提供的**线程安全**的动态数组
- ArrayList
	- 动态数组：基于底层的数组来实现
- LinkedList
	- 双向链表：不需要像上面两种那样调整容量
	- 不利于随机访问

## ArrayList 和 LinkedList 的应用场景

- ==ArrayList==
	- 需要**频繁访问**集合元素的场景
	- 基于数组实现，可以通过索引随机快速访问元素
- ==LinkedList==
	- 频繁进行**插入和删除**操作的场景
	- 基于链表实现，增删只需要调整节点的指针

## ArrayList 集合扩容原理？

1. 计算新容量：扩容到当前容量的 **1.5 倍**，并检查是否超过了**最大容量**限制
2. 创建新的数组
3. 复制元素：通过 `Arrays.copyOf` 将数组的元素复制到新数组上
4. 更新引用：将ArrayList内部指向原数组的**引用指向**新数组

> [!WARNING] 警告
> ArrayList的扩容操作涉及到数组的复制和内存的重新分配，所以在频繁添加大量元素时，扩容操作可能会影响性能。
> 
> 为了减少扩容带来的性能损耗，可以在初始化ArrayList时预分配足够大的容量，避免频繁触发扩容操作。

## 为什么 elementData 会被 transient 关键字修饰?

但实际上 ArrayList 却支持序列化，总结：为了自己实现序列化

因为 elementData 是数组，依靠外部序列化会展示不存在的数据。

## ArrayList 在大量新增的场景下效率一定很低吗？

如果是通过**追加**的方式的话，效率其实并不低，为 O(1) 的时间复杂度。

## 集合中增强 for 内部原理 iterator 反编译介绍 ？

增量 for 是 Java 的**语法糖**，通过反编译之后实际上使用的是集合类中的迭代器 Iterator：
- 以 ArrayList 的迭代器为例，会维护：
	- ==cursor==：迭代器指向**当前元素**的指针
	- ==lastRet==：上一次返回的索引值
	- ==expectedModCount==：迭代器中维护的**修改次数**，初始和 modCount 相等
- 操作
	- *add*：cursor = cursor + 1，lastRet = i
	- *remove*：cursor = lastRet，lastRet = -1
	- *hasNext*：cursor != size

## 集合中的快速失败(fail-fast)机制？

java.util 包下的集合类**都是**快速失败的，正确的用法是使用迭代器`Iterator#remove`方法

- 定义
	- 当使用迭代器遍历元素时，如果使用 List 的 add/remove 方法操作
	- 将会抛出 ConcurrentModificationException 异常
- 原因
	- 这是因为在迭代器执行 next 遍历时，会执行检查：
		- 迭代器保存的修改次数 expectedModCount 变量是否和 List 的 modCount 相等
		- 如果**不相等**，则说明迭代期间**发生了修改**，则抛出异常
- 作用
	- 是一种**错误检测**机制，防止出现集合的**并发修改**错误