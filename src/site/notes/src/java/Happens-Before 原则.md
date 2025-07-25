---
{"dg-publish":true,"dg-permalink":"Happens-Before 原则","permalink":"/Happens-Before 原则/"}
---


约束编译器的优化行为，要求编译器优化必须遵循该原则，从而保证程序的**可见性**。

（`→` 表示对其可见）

1. 程序顺序性原则
	- 前面的代码 → 后边的代码
2. volatile 遍历原则
	- 对 volatile 遍历的写操作 → 读操作
3. 传递性
	- A → B、B → C，则 A → C
4. 管程锁定原则
	- 解锁之前做的操作 → 其他线程再次加锁时的操作
	- 所以 *synchronized* lock 前会**清空工作内存**，unlock 也会把最新数据**同步到主内存**
5. 线程 start 原则
	- 主线程启动子线程前的操作 → 子线程启动后
6. 线程 join 原则
	- 子线程执行时的操作 → 主线程 join 等待线程 B 完成时