---
{"dg-publish":true,"dg-permalink":"Dubbo 源码中的一些概念","permalink":"/Dubbo 源码中的一些概念/"}
---


#Java #Dubbo #中间件

## URL

Dubbo 使用 URL 作为配置载体，作为**配置信息的统一格式**。

所有的拓展点都是通过 URL 获取配置。

## Invoker

Invoker 是一个非常重要的模型，在 Consumer 和 Producer 的客户端中都会有 Invoker。

Invoker 是实体域，它**是 Dubbo 的核心模型**，其它模型都向它靠扰，或转换成它。

它**代表一个可执行体**，可向它发起 invoke 调用，它有可能是：
- 一个**本地**的实现；
- 一个**远程**的实现；
- 一个**集群**实现。