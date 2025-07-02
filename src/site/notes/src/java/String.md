---
{"dg-publish":true,"dg-permalink":"String","permalink":"/String/"}
---


#Java 

## String 为什么要设计为不可变的？不可变有什么好处？

- 可以缓存 Hash 值，只需要一次计算
- 字符串常量池的需要
- 不可变性具备**线程安全**

扩展阅读：
- [不用反射，能否实现一个方法，调换两个 String 对象的实际值？](https://mp.weixin.qq.com/s/goH_MKPgSWm0u_h4z8S7YQ)

## String/StringBuffer/StringBuilder

StringBuffer 实现原理：
- 修改字符序列：底层利用可修改的数组，继承自 `AbstractStringBuilder`
- 线程安全：**所有修改数据的方法**都被 *synchronized* 所修饰

StringBuilder 实现原理：
- 和 StringBuffer 类似，都是继承自 `AbstractStringBuilder`
- **不是**线程安全

> [!caution] 
> 1. 初始化时最好显式地指定大小，否则触发扩容后要进行 array copy，影响性能和浪费内存
> 2. 在之后的 JDK 版本中，**即使直接使用字符串拼接**也会被编译器优化为 `StringBuilder` 

## String 底层结构在不同 JDK 版本中的变化？

![Pasted image 20220901210154.png](/img/user/attachments/images/Pasted%20image%2020220901210154.png)

- JDK7/8：降低内存占用
- JDK9：底层数组替换为 byte 类型，达到更小的内存占用

[字符串常量池在 JDK 1.6、1.7、1.8 中的变化](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FString%20JVM%20%E5%B8%B8%E9%87%8F%E6%B1%A0%E7%9A%84%E7%89%88%E6%9C%AC%E5%8F%98%E5%8C%96)

[几张图彻底理解Java字符串常量池、String.intern()（非复制粘贴，准确解释）](https://blog.csdn.net/weixin_42310154/article/details/122861042)