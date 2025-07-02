---
{"dg-publish":true,"dg-permalink":"Brian Kernighan 算法","permalink":"/Brian Kernighan 算法/"}
---


#算法 

## 概念

> [!QUOTE] 
> 对任何一个数 n，n & ( n − 1 ) 的结果是 n 的比特位**最右端的 1 变为 0** 的结果。

`n & (n − 1)` 满足下面的规律，即可以**消去最右边的 1**：

![Pasted image 20220831203446.png](/img/user/attachments/images/Pasted%20image%2020220831203446.png)

## 应用

根据这个规则，如果我们每做一次 `n &= n - 1` 则代表数字 N 上存在一位的 1

直到 n = 0 ，执行多少次则代表存在多少位 1：

```java
public int hammingWeight(int n) {
    int res = 0;
    while(n != 0) {
        res++;
        n &= n - 1;
    }
    return res;
}
```

