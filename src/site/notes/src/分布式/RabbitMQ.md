---
{"dg-publish":true,"dg-permalink":"RabbitMQ","permalink":"/RabbitMQ/"}
---


#消息队列 #中间件 

## 如何处理消息丢失的情况？

- Producer：通过 RabbitMQ 自带的事务（同步，影响性能）或者 confirm 模式（异步）
- Broker：开启 RabbitMQ 的持久化，消息落地后将写入磁盘
- Consumer：由消费者手动确认消息 ACK