---
{"dg-publish":true,"dg-permalink":"Spring","permalink":"/Spring/"}
---


#Java #Spring

## 谈谈 Spring IoC 的理解，原理与实现？

IoC （*Inversion of Control*）即依赖反转，是一种设计的**思想**。即：
- 用来依赖对象之间的**解耦**
- 依赖的对象不应该由自己来创建（控制权），而是交给**第三方**组件

而 DI（*Dependency injection*）即依赖注入，是 IoC 的**具体实现**，如 Spring。即：
- 不必由自己创建对象，只需要描述如何创建，以及**定义好依赖**
- 第三方框架（如Spring）会自动注入

而[[src/软件设计/依赖倒置原则\|依赖倒置原则]]是面向接口编程中的一个原则，让高层（核心业务）屏蔽了底层的（技术细节）依赖。

## 谈一下spring IOc的底层实现

- 工厂模式
- 反射机制

## 描述一下 Bean 的生命周期？

代码参考：[beanInitialization - HelloService.java](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/library/spring/beanInitialization/HelloService.java)

![spring-bean-initialization-0.png|500](/img/user/attachments/images/spring-bean-initialization-0.png)

## Spring Bean 有哪些 scope？

1. Singleton（单例）：默认的 scope，Spring 容器中只会**存在唯一**的实例；
2. Prototype（原型）：每次请求**都会创建一个新**的实例；
3. Request（请求）：每次 HTTP 请求都会创建新的实例，只对**当前 HTTP 请求**有效；
4. Session（Session）：每次 HTTP 请求都会创建新的实例，只对**当前 HTTP Session** 有效；
5. Globalsession

## Spring 中有几种注入方式？

一共有三种注入方式：

1. 变量注入
2. 构造函数注入
3. setter 注入

当设值注入与构造注入同时存在时，**先执行设置注入，在执行构造注入。**

## Spring 是如何解决循环依赖的问题的？

[Spring 循环依赖问题](Spring%20循环依赖问题.md)

## BeanFactory 与 FactoryBean 有什么区别？

## Spring中用到的设计模式？

1. **简单工厂模式**：如 Spring 中的的 *BeanFactory* 实现。
2. **单例模式**：如 Spring 的 Bean 默认是单例的；
3. **代理模式**：如 Spring 实现的 AOP，就是动态代理；
4. **模板方法模式**：JdbcTemplate、hibernateTemplate 等；
5. **观察者模式**：Spring Event 事件驱动框架。

## Spring AOP 的理解？

AOP 能够将那些**与业务无关**，却为业务模块**所共同调用的逻辑或责任**(例如事务处理、日志管理、权限控制等)封装起来：
1. 便于减少系统的重复代码；
2. 降低模块间的耦合度；
3. 有利于未来的可拓展性和可维护性。

概念：

Aspect（切面），是由切点和通知组成：
- PointCut（切点）：**程序在运行的时间点**，如：如方法的执行、异常的抛出等；
- Advice（通知）：**在切面做的动作**，如打印日志等；

## Spring 的 AOP 的底层实现原理？

1. 核心的原理：通过**动态代理**来实现的。
2. 会有两种方式，取决于被代理的对象**是否实现了接口**：
	- 有：使用 JDK 内置的 Proxy ；
	- 没有：使用 Cglib 生成被代理对象的子类；
3. 区别：
	- *JDK Proxy*
		- 自带的**反射机制**（在**运行时期间**生成的源码文件）来实现
		- 缺点：只能代理实现了接口的类
	- *cglib*：
		- 基于 **ASM** 实现：通过字节码增强生成代理类（被代理类的子类），来调用被代理类（父类）
		- 性能高于 JDK proxy

原理见：[Java 反射](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86)

## Spring AOP 和 AspectJ AOP 有什么区别？

1. Spring AOP，不论是 JDK 内置还是 cglib
	- **动态**织入，原理是动态代理
	- 仅支持方法执行的切入点
2. AspectJ
	- **静态**织入，原理是静态代理，会在编译时、编译后和加载时完成织入
	- 支持所有切入点（字段、方法、构造函数等）
	- 效率更高

[Cglib,asm,Aspect，JDK原生的代理模式这四种有哪些差别，原理各是什么？](https://www.zhihu.com/question/40777626)

## @Autowired 与 @Resource 的区别

1. *@Autowired*
	- Spring 内置的注解
	- 默认通过**类型**来注入
	- **装配可选**：默认要求依赖对象必须存在，可通过 *required* 属性设置不必须
	- 如果要通过 name 来转配，需要配合 `@Qualifier`
2. *@Resource*
	- 是 **javax** 包定义的注解；
	- 支持多种注入方式：
		- 指定 name：按名称查找；
		- 指定 type：按类型查找；
		- name、type 都没有指定：优先**名称**查找，找不到进行类型匹配。

## Spring DeferredResult 原理

1. 在处理 Controller 的返回值时，如果是异步类型（DeferredResult、CompletableFuture、Mono 等），将**不会立即响应客户端**，而是将请求挂起
2. 直到异步任务超时或设置了结果值，才响应客户端
	- 如果返回的 Mono，Spring 将会自动订阅，并将结果值设置到 DeferredResult 中
	- 请求的**线程将被被释放**，用于处理新的请求，提高服务端的吞吐量
3. 最后，再通过 WebAsyncManager 进行请求的再分发（dispatch）
	- 超时处理器
	- 错误处理器

## Spring 的事务是如何回滚的？

## 谈一下 Spring 事务传播？

## Spring MVC 的运行流程？

![](/img/user/attachments/images/Pasted image 20220307165240.png)

## SpringBoot 相比有什么优点？

- 独立部署
- 简化配置
- 自动配置
- 无 XML

