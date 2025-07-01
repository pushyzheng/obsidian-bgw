---
{"dg-publish":true,"dg-permalink":"DNS","permalink":"/DNS/"}
---


#计算机网络 #HTTP #应用层协议 

## DNS 使用的什么传输层协议？

DNS 既使用了 UDP，也使用了 TCP：

1. **区域传送**（主域名服务器向辅助域名服务器**传送变化数据**）使用的是 TCP；
2. **查询域名**（域名解析）时是 UDP。

## DNS 域名解析都有哪几种方式？

DNS 查询的方式使用了递归 + 迭代的方式：

![[Pasted image 20220906095803.png\|700]]