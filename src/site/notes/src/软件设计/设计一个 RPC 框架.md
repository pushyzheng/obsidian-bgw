---
{"dg-publish":true,"dg-permalink":"设计一个 RPC 框架","permalink":"/设计一个 RPC 框架/"}
---


#分布式 #软件设计 

## 服务端和注册中心

1. **网络通信**：例如高性能、高并发的 NIO 框架 Netty；
2. **序列化**：需要比较高效以及压缩好的序列化框架，如 Google 的 Protobuf；
3. **服务治理**：
	- 注册中心：提供可靠的寻址，对 Client 屏蔽 IP 地址，容易进行横向扩容，如 zookeeper 等；
	- 监控中心（可选）：记录服务的调用次数和耗时，上发到监控中心，检测服务的健康度。

客户端：
1. 注册到 Spring 容器
2. Server 实现服务发布
3. Client 实现**动态代理**

## 序列化选型

| Name     | 优点                             | 缺点               | 排名 |
| -------- | -------------------------------- | ------------------ | ---- |
| Protobuf | 速度快、体积小                   | 静态编译，可读性差 | 1    |
| Hessian  | 支持跨语言，适合于发送二进制数据 | 速度慢             | 2    |
| JSON     | 可读性比较强，快速编写           | 性能不高           | 3    |
| XML      | 可读性高，格式统一               | 体积庞大，性能不高 | 4    |
