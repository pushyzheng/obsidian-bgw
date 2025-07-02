---
{"dg-publish":true,"dg-permalink":"Spring 循环依赖问题","permalink":"/Spring 循环依赖问题/"}
---


#Java #Spring 

Spring 对循环依赖的处理有三种情况：
- 构造器循环依赖：spring 无法处理，抛出 BeanCurrentlylnCreationException 异常
- 单例模式下的 setter 循环依赖：**通过 “三级缓存” 来解决循环依赖**
- 非单例循环依赖：无法处理

参考：[Spring 循环依赖原理](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FSpring%20%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96)

## Spring 是如何解决的循环依赖？

使用了三级缓存：
1. 一级：单例池，已经实例化好的对象
2. 二级：提前曝光对象，还没有填充依赖
3. 三级：提前曝光对象工厂对象

假设A、B循环引用：
1. 实例化 A 的时候**就将其放入三级缓存中**，接着填充属性的时候，发现依赖了 B；
2. 同样的流程也是实例化后放入三级缓存，接着去填充属性时又发现自己依赖 A，这时候**从缓存中查找到早期暴露的 A**：
	- 没有 AOP 代理的话：直接将 A 的原始对象注入 B，完成 B 的初始化后，进行属性填充和初始化，这时候 B 完成后，就去完成剩下的 A 的步骤；
	- 如果有 AOP 代理：就**进行 AOP 处理获取代理后的对象 A**，注入 B，走剩下的流程。


## 为什么要使用三级缓存呢？二级缓存能解决循环依赖吗？

如果没有 AOP 代理，二级缓存可以解决问题。但是有 AOP 代理的情况下，**只用二级缓存就意味着所有 Bean 在实例化后就要完成 AOP 代理**。

Spring 在设计之初就是通过 *AnnotationAwareAspectJAutoProxyCreator* 这个后置处理器来在 Bean 生命周期的最后一步来完成 AOP 代理。

而不是在实例化后就立马进行 AOP 代理。

## 如何避免出现循环依赖?

-   通过 `@Autowired` 注解自动装配的方式，可以简单粗暴地解决循环依赖的问题；
-   用 `@Lazy(true)` 也能成，但是需要自己考虑场景是否合适

<br>

> https://jishuin.proginn.com/p/763bfbd2c640