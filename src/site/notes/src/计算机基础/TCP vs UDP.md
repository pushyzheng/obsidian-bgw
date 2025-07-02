---
{"dg-publish":true,"dg-permalink":"TCP vs UDP","permalink":"/TCP vs UDP/"}
---


#计算机网络 

1. TCP：
	- 可靠
	- [面向字节流](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2F%E7%BD%91%E7%BB%9C%20-%20%E9%9D%A2%E5%90%91%E5%AD%97%E8%8A%82%E6%B5%81%E5%92%8C%E9%9D%A2%E5%90%91%E6%8A%A5%E6%96%87%E5%8D%8F%E8%AE%AE)（Stream）协议，可以发任意长度的消息
	- 流量控制
2. UDP：
	- 简单
	- 面向**报文**（Datagram）协议
	- 一对多（广播）
	- 效率高
	- 实时性更好（但容忍丢包）

![](/img/user/attachments/images/dbcac3e5-232f-44fe-b76e-de6e1602177f.png)

