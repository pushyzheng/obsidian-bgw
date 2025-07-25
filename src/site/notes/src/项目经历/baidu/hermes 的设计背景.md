---
{"dg-publish":true,"dg-permalink":"hermes 的设计背景","permalink":"/hermes 的设计背景/"}
---


原 PHP 上访问摘要服务使用的 ral 的 event 模式，该模式有个特点：
- 请求发送环节是**串行**的：选择后端、建立链接、发送请求
- 请求等待可以并行

在这个特性下，每增加一个并行的后端，ral就增加一个串行的"请求发送环节"（整体耗时增加1ms）

![Pasted image 20250721160309.png](/img/user/attachments/images/Pasted%20image%2020250721160309.png)

为了解决该问题，引入一个 hermes 转发服务：
- odp与转发服务交互一次，hermes 拆分多个请求
- hermes 并行发送给后端，并统一接收

![Pasted image 20250721160415.png](/img/user/attachments/images/Pasted%20image%2020250721160415.png)