---
{"dg-publish":true,"dg-permalink":"Java Index","permalink":"/Java Index/"}
---


## 基础

- 基本类型
	- [[src/java/Java String\|String]]
		- [[src/java/Java String#String 为什么要设计为不可变的？不可变有什么好处？\|String 为什么是不可变的]]
		- [[src/java/Java String#String/StringBuffer/StringBuilder\|String/StringBuffer/StringBuilder 的区别]]
	- [[src/java/Java 基础#什么是包装类型？以及拆箱和装箱？\|包装类型的拆箱和装箱]]
	- [[src/java/Java 基础#Integer 的缓存机制\|Integer 的缓存机制]]
- 关键字
	- [[src/java/Java 基础#Java 中 final 作用是什么？\|final 修饰符的作用]]
	- [[src/java/Java 基础#== 和 equals 的区别？\|== 和 equals 的区别]]
- [[src/java/Java 面向对象\|面向对象]]
	- [[src/java/Java 面向对象#面向对象的三大特性?\|面向对象的三大特性]]
	- [[src/java/Java 面向对象#谈谈你对多态的理解？\|对多态的理解]]
	- [[src/java/Java 面向对象#抽象类和普通类区别?\|抽象类和普通类区别]]
- [[src/java/Java 基础#谈谈你对泛型的理解？\|对泛型的理解]]
- [[src/java/Java 基础#什么是反射？\|什么是反射]]
- [[src/java/Java 基础#能讲一讲Java注解的原理吗?\|注解的原理]]
- [[src/java/Java 基础#深拷贝和浅拷贝的区别？\|深拷贝和浅拷贝的区别]]
	- [[src/java/Java 基础#实现深拷贝的三种方法是什么？\|实现深拷贝的三种方法]]
- [[src/java/Java Exception\|异常]]
	- [[src/java/Java Exception#介绍一下Java异常\|介绍一下Java异常、Java 异常的分类]]
	- [[src/java/Java Exception#ClassNotFoundException 和 NoClassDefFoundError 的区别\|ClassNotFoundException 和 NoClassDefFoundError 的区别]]
- [[src/java/Java IO\|I/O]]
	- [[src/java/Java IO#BIO、NIO、AIO 有什么区别？\|BIO、NIO、AIO 有什么区别]]
	- [[src/java/Java IO#NIO 是怎么实现的？\|NIO 是怎么实现的]]

## 集合

- [[src/java/Java 集合#集合中的快速失败(fail-fast)机制？\|快速失败(fail-fast)机制]]
- List
	- [[src/java/Java 集合#ArrayList 和 LinkedList 的应用场景\|ArrayList 和 LinkedList 的应用场景]]
	- [[src/java/Java 集合#ArrayList 集合扩容原理？\|ArrayList 集合扩容原理]]
- Map
	- [[src/java/Java Map#HashMap 和 HashTable 的区别？\|HashMap 和 HashTable 的区别]]
	- [[src/java/Java Map#HashMap 的实现原理？\|HashMap 的实现原理]]
	- [[src/java/Java Map#说一下 HashMap 的 put 过程？\|HashMap 的 put 过程]]
	- [[src/java/Java Map#HashMap 的扩容过程？\|HashMap 的扩容过程]]
	- [[src/java/Java Map#为什么 HashMap 的容量要是 2 的倍数？\|为什么 HashMap 的容量要是 2 的倍数]]
- 并发集合类
	- [[src/java/Java 并发集合类#ConcurrentHashMap 是如何实现的？\|ConcurrentHashMap 是如何实现的]]

## 并发

- 线程
	- [[src/java/Java 线程#Java 线程的状态都有哪些？\|Java 线程的状态]]
	- [[src/java/Java 线程#什么是守护线程？\|什么是守护线程]]
	- [[src/java/Java 线程#wait 和 sleep 有什么区别？\|wait 和 sleep 有什么区别]]
- [[src/java/Java 线程池\|线程池]]
	- [[src/java/Java 线程池#创建线程池都有哪些参数？\|创建线程池都有哪些参数]]
	- [[src/java/Java 线程池#线程个数如何设置?\|线程个数如何设置]]
	- [[src/java/Java 线程池#线程池中都有哪些拒绝策略的？\|线程池中都有哪些拒绝策略]]
- [[src/java/Java 并发相关\|并发]]
	- [[src/java/Java 并发相关#并发的起因？\|并发的起因？为什么会有并发问题?]]
	- [[src/java/Java 并发相关#什么是 CAS？它会存在什么问题？\|CAS]]
	- [[src/java/Happens-Before 原则\|Happens-Before 原则]]

## JVM

- [[src/java/JVM 相关#JVM 的运行时数据区有哪些？都有什么作用？\|JVM 的运行时数据区]]
	- [[src/java/JVM 相关#JVM 的堆分为哪几部分呢？\|堆的组成]]
- [[src/java/JVM 相关#JMM 中内存模型是怎样的？\|JMM 内存模型]]
- [[src/java/JVM 相关#Java 方法栈（线程内存模型）是怎样的？\|方法栈、线程内存模型]]
- [[src/java/JVM 逃逸分析\|逃逸分析]]
	- [[src/java/JVM 逃逸分析#逃逸分析都有哪些优化？\|逃逸分析都有哪些优化]]
- [[src/java/类加载机制常见问题\|类加载机制]]
	- [[src/java/类加载机制常见问题#1. 类加载机制的过程？\|类加载机制的过程]]
	- [[src/java/类加载机制常见问题#2. 什么是双亲委派机制？\|双亲委派机制]]
- [[src/java/JVM 相关#什么是指令重排序？\|指令重排序]]
	- [[src/java/JVM 相关#volatile 是怎么防止指令重排序的？\|volatile 是怎么防止指令重排序的]]
- [[src/java/JVM 垃圾回收\|垃圾回收（GC）]]
	- [[src/java/JVM 垃圾回收#Java 中垃圾回收机制中如何判断对象需要回收?\|如何判断对象需要回收]]
	- [[src/java/JVM 垃圾回收#常见的垃圾收集算法\|常见的垃圾收集算法]]、[[src/java/JVM 垃圾回收#能说一下分代收集算法吗？\|分代收集算法]]
	- [[src/java/JVM 垃圾回收#垃圾回收器都有哪些？\|常见的垃圾回收器]]
		- [[src/java/JVM 垃圾回收#垃圾回收器 CMS 和 G1的区别?\|CMS 和 G1的区别]]
		- [[src/java/G1#G1 收集器有哪些特点?\|G1 收集器有哪些特点]]
	- [[src/java/JVM 垃圾回收#minorGC、majorGC、FullGC 的区别？\|minorGC、majorGC、FullGC 的区别]]