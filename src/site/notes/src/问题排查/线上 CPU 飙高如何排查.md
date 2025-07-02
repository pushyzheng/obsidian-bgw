---
{"dg-publish":true,"dg-permalink":"线上 CPU 飙高如何排查","permalink":"/线上 CPU 飙高如何排查/"}
---


#JVM #Java #线上问题排查

先使用 top 命令来排查是哪个进程的 CPU 占用出了问题，如果确实是 Java 的话：

- 使用 jstat 查看哪个线程占用最高，找到线程 ID；
- 再通过 jstack 排查线程堆栈，找到运行的代码。

![](/img/user/attachments/images/Pasted image 20220219111937.png)

一般情况下：死循环：很容易从代码中发现，如：

![Pasted image 20220219112408.png|500](/img/user/attachments/images/Pasted%20image%2020220219112408.png)

 死锁则很容易从 jstack 中的死锁检测中看出：
 
 ![Pasted image 20220219112539.png](/img/user/attachments/images/Pasted%20image%2020220219112539.png)