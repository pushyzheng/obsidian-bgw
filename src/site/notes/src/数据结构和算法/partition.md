---
{"dg-publish":true,"dg-permalink":"partition","permalink":"/partition/"}
---


#算法 

## 概念

Partition 方法是[快速排序](排序.md#快速排序)的关键，通常也用来解决 [TopK 问题](TopK%20问题.md)。

定义一个 *partition* 方法，传入数组的上限和下限：

```java
int partition(int[] nums, int low, int high);
```

那么调用函数之后，返回的 *index* 中，能保证：
- *index* **左**边的元素都**小**于 *nums[index]*
- *index* **右**边的元素都**大**于 *nums[index]*

## 实现原理

### 对撞指针

首先需要选取一个基准点 *pivot*（很多种选法：最左边、左右边、中间、随机等），即最终返回的 *num[index]* 的值。

假设选取最左边的 low 作为基准值，那么左右两个对撞指针开始移动：
- *high*（右指针）：从右到左找到比 pivot **小**的数
- *low*（左指针）：从左到右找到比 pivot **大**的数
- 结束条件为 *low >= high*

动画：

![](屏幕录制2022-03-27%2010.09.26.mov)

[参考代码](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/algorithm/partition/impl/Partitions1.java)

### 快慢指针

我们也可以使用从左到右移动的快慢指针来实现，此时为了方便，我们选择最右边的 *high*  作为基准点。

以及两个左右指针：
- *i*（慢指针）：用于替换比 pivot 小的数
- *j*（快指针）：用于遍历，找到比 pivot 大的数

且：
- 每当 *nums[j] < pivot* 时，就将 *++i* （比 pivot 大的数）和 *j* 的的元素进行交换
- 而 *num[j] > pivot* 时，继续遍历，直到找到下一个比 pivot 小的数

[参考代码](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/algorithm/partition/impl/Partitions2.java)

https://www.geeksforgeeks.org/quick-sort/