---
{"dg-publish":true,"dg-permalink":"为什么 HashMap 的容量要是 2 的倍数？","permalink":"/为什么 HashMap 的容量要是 2 的倍数？/"}
---


#Java #Java集合 #问题 

不论初始化时传入的容量是否为 2 的倍数，HashMap 都会通过 `tableSizeFor` 方法将其转换为最接近的 2 的倍数：

```java
static final int tableSizeFor(int cap) {  
    int n = -1 >>> Integer.numberOfLeadingZeros(cap - 1);  
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;  
}
```

## 原因

在 HashMap 的内部操作中， 通常会通过下面的方式来计算所在的哈希槽的在 tab 数组中的下标位置：

```java
int i;
Node e = tab[i = (n - 1) & hash]
```

原因是通过 “与” 运算来替代取余计算达到极致的性能优化：

```java
int index = hash & (cap - 1)
// 等同于
int index = hash % cap
```

但是**这得保证 cap 值为 2 的倍数，上列的等式才能满足**。

```java
8           // 转换为十进制为: 1000
8 - 1 = 7   // 转换为十进制为: 111
```

假设某个键的哈希值为 10，让该值与 cap - 1 = 7 -> 111 做位与运算，得：

```java
// 当且仅当 1 & 1 为 1，其他均为 0
1010 (10)
0111 (7)
||||  &
0010 (2)
```

其结果与 10 % 8 = 2 结果相同。

## 原理

因为容量为 8 的哈希数组，其索引值的范围为 `[0, 7]` ，而 2 的倍数 - 1 有一个共同的特点：

```java
// 转换为二进制后所有位都为 1

1 -> 1
3 -> 11
7 -> 111
......
```

因为，它相当于**低位掩码**，可以让与它参与 “与” 操作的数其高位全部归零，只保留低位值。

最主要的：**能够保证计算出的值一定比 7 小**（因为 7 已经是二进制三位中最大数了），也就保证了结果值在 `[0, 7]` 区间。