---
{"dg-publish":true,"dg-permalink":"ThreadLocal","permalink":"/ThreadLocal/"}
---


#Java 

## 简单介绍一下 ThreadLocal

1. 提供了线程的局部变量
2. 不会和其他线程的局部变量进行冲突，实现了**线程的数据隔离**

## ThreadLocal 有哪些使用场景呢？

1. 存储上下文信息
	- 代替参数的显式传递
	- 全局存储用户信息
2. 解决线程安全问题
	- 数据库连接，保证线程隔离

## ThreadLocal 的原理是什么？

1. Thread 对象的 threadLocals 是一个 Map
	- key：是对 *ThreadLocal* 对象的弱引用
	- value：则是每个 *ThreadLocal* 存储的值
2. Key 设计成弱引用的原因？
	- 即使栈中对 ThreadLocal 的引用被销毁，*Thread#threadLocals* 中的 **Key 依然对 ThreadLocal 保持强引用**，将不会被回收
	- 如果为弱引用的话，在下次 GC 时将会被回收

## ThreadLocal 应该在什么时间点做释放？

1. 在使用完之后就调用 `remove()` 进行释放

## ThreadLocal 如何在线程间传递？

父子线程可以通过 ***InheritableThreadLocal*** 进行传递。

实现原理是 Thread 在初始化时，会[将父线程的 ThreadLocal 设置到当前线程](https://github.com/openjdk-mirror/jdk/blob/jdk8u/jdk8u/master/src/share/classes/java/lang/Thread.java#L415-L417)。