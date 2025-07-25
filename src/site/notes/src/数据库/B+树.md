---
{"dg-publish":true,"dg-permalink":"B+树","permalink":"/B+树/"}
---


B+树的特征：
1. 非叶子结点不保存数据，仅用来**索引**
2. 只有叶子节点存放真实数据（聚簇索引）
3. 所有节点按照索引键大小排序，构成一个**双向链表**，便于范围查询

![Pasted image 20250723185615.png](/img/user/attachments/images/Pasted%20image%2020250723185615.png)