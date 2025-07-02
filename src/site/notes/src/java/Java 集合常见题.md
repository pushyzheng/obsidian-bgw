---
{"dg-publish":true,"dg-permalink":"Java 集合常见题","permalink":"/Java 集合常见题/"}
---


## 1. 集合中增强 for 内部原理 iterator 反编译介绍 ？

增量 for 是 Java 的语法糖，通过反编译之后实际上使用的是集合类中的**迭代器** *Iterator*。

以 ArrayList 的迭代器为例，会维护：
- *cursor*：迭代器指向**当前元素**的指针；
- *lastRet*：上一次返回的索引值；
- *expectedModCount*：迭代器中维护的**修改次数**，初始和 modCount 相等。

操作：
- *add*：cursor = cursor + 1，lastRet = i；
- *remove*：cursor = lastRet，lastRet = -1；
- *hasNext*：cursor != size

> [!NOTE] 快速失败
> 当 `modCount != expectedModCount` 时，则抛出 *ConcurrentModificationException* 异常。

## 2. 集合中的快速失败(fail-fast)机制？

快速失败是指：当使用迭代器（包括增强 for）遍历元素时：如果使用 **List 的更新**（add、remove）的 API 操作，将会抛出 *ConcurrentModificationException* 异常。

这是因为在迭代器执行 next 遍历时，会执行 *checkForComodification* 检查：
- 迭代器保存的修改次数 *expectedModCount* 变量是否和 List 的 modCount 相等
- 如果不相等，则说明**迭代期间发生了修改**，则抛出异常

![](/img/user/attachments/images/Pasted image 20220324161553.png)

*java.util* 包下的集合类**都是快速失败**的，正确的用法是使用迭代器 **Iterator#remove** 方法。

## 3. ArrayList 集合扩容原理？

扩容到当前容量的 **1.5 倍**，通过 *Arrays.copyOf* 将数组的元素复制到新数组上。

## 4. 为什么 elementData 会被 transient 关键字修饰，但实际上 ArrayList 却支持序列化？

总结：为了自己实现序列化

因为 elementData 是数组，依靠外部序列化会展示不存在的数据。

## 5. ArrayList 在大量新增的场景下效率一定很低吗？

如果是通过**追加**的方式的话，效率其实并不低，为 O(1) 的时间复杂度。

可以看： [ArrayList#add](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FArrayList)

## 6. HashTable 是如何保证并发安全的呢？

通过 ***synchronized* 关键字**修饰 get、put、size 等方法，实现并发安全。

这种实现的方式效率非常的低。

## 9. HashMap 相关

[[src/java/HashMap\|HashMap]]

## 8. HashSet 底层原理和扩容机制介绍？