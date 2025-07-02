---
{"dg-publish":true,"dg-permalink":"MyBatis","permalink":"/MyBatis/"}
---


#Java #Java框架

## 如何避免 SQL 注入的？

1. `#{}`
	- 会进行**预编译处理**，将其替换成 `?`，调用 *PreparedStatement* 来赋值
	- 能够很大程度防止 SQL 注入
2. `${}`
	- 直接将 `${}` 进行**字符串替换**
	- **无法防止** SQL 注入

## MyBatis 有什么优点？

1. 封装 JDBC：相比于 JDBC 减少了开发的工作量，专注于 SQL 本身
2. 灵活性高：纯 SQL 编写，能很好控制 SQL 的执行性能；
3. SQL 与代码解耦：将 SQL 语句在 XML 文件中统一配置，便于管理和解耦
4. 支持对象关系映射：可通过 *ResultMap* 或者 *ResultType* 来映射

## MyBatis 和 Hibernate 相比有什么不同？

1. MyBatis 是一个**半 ORM** 框架，仍然需要自己编写 SQL 语句：
	- 虽然提高工作量
	- 但能提高灵活性
2. Hibernate **数据库无关性好**，不与数据库强绑定，能很好地移植数据库 Server

## MyBatis 框架的缺点？

- SQL 编写工作量大：需要手写 SQL，**增大的开发量**
- SQL 依赖于数据库：数据库**移植性差**，无法随意更换数据库（MySQL -> PG）

## MyBatis 一级缓存如何实现的？

一级缓存，默认是**开启**的。

主要是用于缓存**完全相同的 SQL**，减少多次对数据库的相同查询操作，提高性能。

![](/img/user/attachments/images/Pasted image 20220320215203.png)

一级缓存有两个级别：
- *Session*：一个 MyBatis 会话（**SqlSession**）中有效
- *Statement*：仅对当前 **Statement** 有效

## MyBatis 二级缓存如何实现的？

MyBatis 的二级缓存的作用域是**全局**的，它的优先级会被一级缓存**高**。

默认是**不开启**的，需要进行配置。

![](/img/user/attachments/images/Pasted image 20220320215243.png)