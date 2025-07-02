---
{"dg-publish":true,"dg-permalink":"canal","permalink":"/canal/"}
---


#分布式 #中间件 

https://github.com/alibaba/canal

*Canal* 是阿里巴巴开源的 MySQL binlog 增量订阅和消费组件。

![Pasted image 20220324124311.png|500](/img/user/attachments/images/Pasted%20image%2020220324124311.png)

## 工作原理

Canal 的工作原理是利用 MySQL 的主从复制机制，将自己**伪装成 Slave**，向 MySQL 的 Master 主机发送 dump 协议。

收到 Master 发送的 binlog 后，进行解析，再分发给使用订阅 canal 的主机。