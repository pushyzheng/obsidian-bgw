---
{"dg-publish":true,"dg-permalink":"Java Exception","permalink":"/Java Exception/"}
---


## 介绍一下Java异常

Throwable有两个重要的子类：Error和Exception:

1. ==Error（错误）==
	- 表示运行时环境的错误: 程序**无法处理**的严重问题
	- 如系统崩溃、虚拟机错误：OutOfMemoryError/StackOverflowError
2. ==Exception（异常）==
	- 表示程序本身**可以预料到并且处理**的异常条件, 应当被捕获，并进行处理
	- 区分
		- ==运行时异常==
			- 通常是编码导致的逻辑错误
			- 空指针、数组越界等
		- ==受检异常==
			- 必须显示进行捕获，或者向上抛出，是编译期检查的一部分
			- 文件不存在、类未找到

## finally 使用要注意的地方

- 不要在 finally **抛出异常**，否则会覆盖 try 中的异常
- 不要在 finally 中**执行 return**，否则会提前 return，覆盖掉 try 中的返回值

## ClassNotFoundException 和 NoClassDefFoundError 的区别

而它们的区别为：
- ==ClassNotFoundException==
	- 在**动态加载** Class 时（例如 `Class.forName()`），找不到对应的类
- ==NoClassDefFoundError==
	- 在编译期间可以依赖的类，但是在**运行期间无法找到** Class
	- 通常发生在缺少引入相应的 jar 的场景下

NoClassDefFoundError：

![](/img/user/attachments/images/Pasted image 20220307161431.png)

抛出异常：

![](/img/user/attachments/images/Pasted image 20220307161551.png)