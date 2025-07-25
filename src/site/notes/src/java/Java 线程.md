---
{"dg-publish":true,"dg-permalink":"Java 线程","permalink":"/Java 线程/"}
---


 #Java 

## Java 线程的状态都有哪些？
 
 3. *New*：初始状态，Thread 对象刚被创建时
 4. *Runnable*：就绪状态，等待被 CPU 调度，即调用 `start` 方法后
 5. *Running*：运行状态
 6. 休眠状态
	 - *Blocking*：阻塞
	 - *Waiting*：休眠
	 - *Timed_Waiting*：超时休眠
 7. *Terminated*：终止，生命周期结束
 
## 创建线程的三个方法？
 
 1. 继承自 Thread 类；
 2. 实现 Runnable 接口，并传入到 Thread 对象
 3. 实现 `Callable` 再通过 `FutureTask` 进行封装来实现，该实现方法允许线程有返回值：

## 什么是守护线程？

1. 特性
	- 主线程不会等待所有守护线程执行完毕（后台执行）
	- 守护线程可以自动结束生命周期，会在 JVM 等待所有非守护线程执行完毕退出时，**自动结束**
2. 通过 `setDaemon` 来将一个线程变为守护线程
3. 场景
	- 后台任务：垃圾回收线程
 
## Runnable 的设计模式

采用**策略模式**：
- 将线程的控制本身和业务逻辑的运行分离开来
	- Thread：线程**本身**相关的职责和控制
	- Runnable：**实际的**业务逻辑的部分
- 达到职责分明，功能单一的原则
 
## Thread 类中的 yield 和 sleep 有什么区别？

1. sleep：当前线程从执行状态进入休眠状态
2. yield：可以暂时让出 CPU 使用权：从执行状态（运行状态）变为可执行态（就绪状态）

## 怎么检测一个线程是否拥有锁?

在 *java.lang.Thread* 中有一个方法叫 `holdsLock(Object obj)`。

它返回 true 说明：当前线程拥有传入对象的锁。

## wait 和 sleep 有什么区别？

1. ==wait==
	- 会**释放**锁
	- 用途：线程间协调，释放锁让其他线程工作
2. ==sleep==
	- 会释放 CPU，但**不会**释放锁
	- 用途：暂停线程执行，不涉及锁协作