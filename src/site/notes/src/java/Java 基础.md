---
{"dg-publish":true,"dg-permalink":"Java 基础","permalink":"/Java 基础/"}
---


#Java #Java基础 

## 基本类型

| Name     | 所占比特                       | 
| -------- | ------------------------------ |
| byte     | 8                              |
| short    | 16                             |
| char     | 16                             |
| int      | 32                             |
| float    | 32                             |
| long     | 64                             |
| double   | 64                             |
| boolean  | 1                              |
| 引用类型 | 64 位平台上：8 位，32 位：4 位 |

数据范围计算：
- 对于 int 来说，第一位是标志位（正数为 0， 负数为 1），所以只有 31 位可以表示
- 最小值：`-2 ^ 31`
- 最大值：`-2 ^ 31 + 1`

### 什么是包装类型？以及拆箱和装箱？

Integer 是 int 基本类型的包装类型，会在编译期进行[自动的拆箱和装箱](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20%E7%B1%BB%E5%9E%8B%E8%87%AA%E5%8A%A8%E6%8B%86%E7%AE%B1%E5%92%8C%E8%A3%85%E7%AE%B1)，**本质上是一种语法糖**，即：
- 装箱：基本类型 -> 包装类型，调用 **valueOf** 方法
- 拆箱：包装类型 -> 基本类型，调用 **intValue** 方法

[Integer](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20-%20Integer) **存在缓存机制**，即 valueOf 会先从内部的缓存中获取数据，缓存范围在：`[-128, 127]` 内。

### Java 是值传递还是引用传递？

Java 不论是基本类型还是引用类型，都是**值传递**，且只有值传递。

引用类型的值存储的是：**对象在堆中的地址**。

![Pasted image 20220328122200.png|400](/img/user/attachments/images/Pasted%20image%2020220328122200.png)

### float 和 double 精度丢失问题？如何去避免？

1. 精度丢失原因
	- float 和 double 都是浮点运算
	- 小数点转二进制**丢失精度**
2. 如何避免
	- 使用 *String* 参数的构造方法来创建 **BigDecmal**
	- 原理
		- 不可变的
		- 把十进制小数扩大 N 倍让它**在整数上进行计算**，并保留相应的精度信息

### JDK 8 的新特性

1. Lambda 表达式
	- 函数式编程，允许将方法作为方法进行传入
2. 方法引用
	- 语法糖，可以直接引用已有对象和类的方法或构造器，与 lambda 配合使用
3. Stream API
	- 函数式编程风格、流抽象的方式来处理集合等数据结构
4. Optional 类
	- 避免空指针异常
5. Date Time API
	- 加强对日期与时间的处理 LocalDateTime、LocalDate、LocalTime 等

## 关键字

### 修饰符

| Name      | 可见范围     |
| --------- | ------------ |
| private   | 当前类       |
| protected | 当前类和子类 |
| default   | 同一个包     |
| public    | 所有         | 

### == 和 equals 的区别？

== ：判断两个对象是否是**同一个对象**：
- 基础类型：比较**值**
- 引用类型：比较**内存地址**

*equals* 判断两个对象**是否「相等」**：
- 如果没有重写：使用的值 *Object#equals*，和 == 的效果是一致的
- 重写：逻辑通常比较的是「**内容**」

### ClassNotFoundException 和 NoClassDefFoundError 的区别

在 [Java 异常](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20%E5%BC%82%E5%B8%B8) 分为 Error 和 Exception，代表着：
- *Error*：**无法恢复**的错误，通常是 JVM **自身的错误**
- *Exception*：可以**进行捕获并处理**的错误

而它们的区别为：
- *ClassNotFoundException*：在**动态加载** Class 时（例如 `Class.forName()`），找不到对应的类；
- *NoClassDefFoundError*：在编译期间可以依赖的类，但是在**运行期间无法找到** Class，通常发生在缺少引入相应的 jar 的场景下。

![](/img/user/attachments/images/Pasted image 20220307161431.png)

抛出异常：

![](/img/user/attachments/images/Pasted image 20220307161551.png)
### final、finally、finalize的区别和用法?

-   *final*：是一个修饰符，可以被用来修饰变量（不能被**重新赋值**）、方法（不能**重写**）、类（不能**继承**）；
-   *finally*：是一个关键字，其构造的代码块会在 try-catch 语句最后执行；
-   *finalize*：是一个 Object 定义的方法，**用于在 GC 将对象清除前做必要的清理工作**，见：[FinalizeExample](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/builtin/jvm/FinalizeExample.java)

> [!WARNING] finally 使用要注意的地方？
> - 不要在 finally **抛出异常**，否则会覆盖 try 中的异常；
> - 不要在 finally 中**执行 return**，否则会提前 return，覆盖掉 try 中的返回值；

## String

[[src/java/String\|String]]

## 类和对象

### 加载顺序

静态变量 = 静态初始化块（仅在主类中执行一次） > 成员变量 > 初始化块 > 构造器

> [!INFO] 
> 由于静态变量和静态代码块的优先级相同，则**谁在前先执行谁**

如果存在继承的情况下，初始化顺序为：
1. 父类（静态变量、静态代码块）
2. 子类（静态变量、静态代码块）
3. 父类（实例变量、构造块）
4. 父类（构造函数）
5. 子类（实例变量、构造块）
6. 子类（构造函数）

### 内部类

| 内部类类型 | 可访问的资源           | 特点                                  |
| ---------- | ---------------------- | ------------------------------------- |
| 静态       | 静态资源               | -                                     |
| 成员       | 所有资源               | 本身不能再定义静态资源（JDK 16 以下） | 
| 局部       | 与当前方法访问资源相同 | -                                     |
| 匿名       | 与当前方法访问资源相同 | 无构造器、只能被创建一个实例          |

## 注解

注解从本质上来说就是一个**标记**，在反射解析时可以获取到这个标记以及设置的值。

元注解（用于注解其他注解）：
1. @Target
	- 限制修饰的对象范围（Field、Class、Method...）
2. @Retention
	- 指定注解的**生命周期**（存在阶段）
	- SOURCE（源码）：给编译器使用，不会写入 class 文件
	- CLASS（类加载）：类加载阶段丢失
	- RUNTIME（运行）：永久保存，运行期间可以反射获取
3. @Inherited
	- 被标注的类型是被**继承**的
4. @Documented

## 反射

反射就是 Java 提供**在运行期可以得到对象信息的能力**，包括：
- 属性
- 方法
- 注解
- 调用其方法

### 原理

Java 会在编译期状态所有类，并将其元信息保存至 **Class 类对象**中。

因此在运行时可以通过类或者对象，获取到对应的 「Class 类对象」，从而进行反射。

## 面向对象

### 三大特性

1. 封装：对不可信的信息进行隐藏
2. 继承：
3. 多态

### 谈谈你对多态的理解？

多态就是**对象在运行过程中存在不同的状态**，有三个前提：
1. 要有继承关系
2. 子类要重写覆盖方法
3. 父类类型引用指向子类对象

把不同的子类对象都当做父类看，屏蔽不同子类对象之间的差异，写出通用的代码。

### 谈谈你对泛型的理解？

优点：
- **代码复用**：不用为每个类型都编写一份相同的代码；
- **类型安全**：不用强制转换。

> [!IMPORTANT] 
> JDK 1.5 之后，为了兼容之前的版本，会在运行时进行[泛型擦除](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FJava%20%E6%B3%9B%E5%9E%8B)，即泛型**只存在于编译器**。

### 重载与重写的区别?

重载是指：方法名相同，但参数类型、顺序、个数不同

> [!IMPORTANT] 
> **返回值类型**的不同，不作为重载的条件。

重写，指子类重写父类的方法要求：
- **方法签名**（方法名、参数类型、返回值等：必须相同，且包含 `@override` 注解
- **异常类型**：与父类相同，或是其子类
- **访问权限**：不能低于父类

### 深拷贝和浅拷贝的区别？

- 深拷贝：完全拷贝一个对象，堆中的**引用对象也复制一份**
- 浅拷贝：仅拷贝基本类型和**引用**

如图：

![Pasted image 20220328125735.png](/img/user/attachments/images/Pasted%20image%2020220328125735.png)

## 序列化

### 说一下 serialVersionUID 的理解？

1. 如何生成的？
	- 根据类名、成员方法及属性生成的哈希值
	- 如果没有显式定义的话，JDK 会默认生成一个
2. 作用
	- 用来验证是否兼容
		- 在反序列时，会将字节流的  *serialVersionUID* 与反序列化的实体类的 *serialVersionUID* 相比，如果不一致将会失败
	- 用来控制是否兼容
		- 如果一个类可以向下兼容，则不修改该值
		- 否则，递增该值