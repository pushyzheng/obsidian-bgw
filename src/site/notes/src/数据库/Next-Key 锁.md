---
{"dg-publish":true,"dg-permalink":"Next-Key Lock","permalink":"/Next-Key Lock/"}
---


## 什么是 Next-Key 锁？

Next-Key Lock 称为临键锁，是 Record Lock + [[src/数据库/间隙锁\|Gap Lock]] 的组合，锁定一个范围，并且锁定记录本身：
- 锁定某条记录**本身**以及它**前面的间隙**
- 即能保护该记录，又能阻止了其他事务将新记录插入到被保护记录前面的间隙中


假设:
1. 表中有一个范围 id 为（3，5] 的 next-key lock
2. 那么其他事务即不能插入 id = 4 记录，也不能修改 id = 5 这条记录

![Pasted image 20250723194414.png](/img/user/attachments/images/Pasted%20image%2020250723194414.png)