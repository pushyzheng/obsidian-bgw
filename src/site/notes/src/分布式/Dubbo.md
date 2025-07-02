---
{"dg-publish":true,"dg-permalink":"Dubbo","permalink":"/Dubbo/"}
---


#Java #Dubbo #中间件

## 知道什么是 RPC 么？

RPC（*Remote Procedure Call*）即远程过程调用：
1. 分布式应用框架下的产物。
2. 解决跨不同主机之间的接口调用，但是会带来一系列的分布式难题
3. 与此相对的是：同一主机之间的调用，即「本地过程调用」

## 那为什么要有 RPC，HTTP 不好么？

1. RPC 和 HTTP 并不是一个并行的概念
	- 它们都是为了实现通信
	- RPC 也支持通过基于 HTTP 作为通信的基础
2. RPC 的概念（1982）也是早于 HTTP 概念（1991）先出现的
3. RPC 的优势在于
	- 可定制化：可以**自定义协议**、加密算法等，而且**安全**
	- 效率：使用高效的 TCP 序列化协议，提高效率

## Dubbo 支持那些协议？

- Dubbo 2.0
	1. dubbo（默认）：**单一长连接，NIO 异步**，基于 TCP 协议、**Hessian 序列化**；
	2. http：基于 HTTP 表单提交，适用于**需要给应用程序和浏览器 JS 调用的场景**；
	3. rmi：Java 的标准的 rmi 协议实现；
	4. hessian
	5. Redis
- Dubbo 3.0+
	- 新增协议 **Triple**：基于 **HTTP/2** 上构建的 RPC 协议，完全**兼容 gRPC**；
	- 更适合云原生场景；
	- 推荐配合 Protobuf；
	- 流式通信支持

## 说说你对 Dubbo 的了解？

![Pasted image 20220309113955.png|600](/img/user/attachments/images/Pasted%20image%2020220309113955.png)

1. 注册中心
	- Zookeeper（推荐）：基于 Zookeeper 的 **watch 机制**实现数据变更
	- Redis：使用**哈希表**来存储，并基于 Redis 的发布/订阅模式通知数据变更；
	- Multicast
2. Producer 服务容器启动后，向注册中心**注册服务接口**；
3. Consumer 向注册中心**订阅所需服务**（拉取 IP 地址），并监听配置的改变；
4. Consumer 向 Producer **发起调用**；

## 看过源码，那说下服务暴露/服务引入/服务调用的流程？

[Dubbo 源码中的一些概念](Dubbo%20源码中的一些概念.md)

### 服务暴露

1. 发生在 Spring 发布 *ContextRefreshedEvent* 事件**之后**；
2. 检查参数，**拼接 Dubbo URL**；
3. **导出服务**到本地和远程：
	- 使用 javassist 进行动态代理**生成 Invoker**：用来封装真实的实现类；
	- 执行 export 导出后：**生成 Exporter 对象（包含 Invoker）后存入 Map 中**，供之后调用。
4. 向注册中心（Zookeeper）**注册服务**。

### 服务引入

服务引入有两个时机：
- 饿汉式：Spring 执行 ReferenceBean 的 afterPropertiesSet 开始引用；
- 懒汉式（**默认**）：对应的服务**被注入到其他 Bean 中**时，才开始引用。

1. 向注册中心注册 Consumer 的信息，并**获取服务提供者的相关配置**；
2. **创建每个服务对应的 Invoker**，用于远程通信；
3. **通过 Cluster 来包装 Invoker**，使其具备集群容错的能力；
4. **生成代理类**，注入 Spring 容器。

### 服务调用

![](/img/user/attachments/images/Pasted image 20220309152616.png)

1. Consumer **调用代理对象**，执行远程的服务调用；
2. 根据容错机制、负载均衡**匹配到对应的 Invoker**，发起调用；

Producer 接收到请求后，找到之前暴露存储的 Map：
1. 将请求发送到 Dispatcher，再下发到线程中执行调用；
2. 得到**对应的 Exporter**
3. **调用真正的实现类**，并组装结果返回。

## 知道什么是 SPI 嘛？

[SPI](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FSPI) 即：*Service Provider Interface*，服务发现接口。主要被用于**外部的拓展和解耦**。

Dubbo 自定义了一套 SPI 机制：
- **按需加载**：每个服务的实现都有名称；
- 减少内存占用

## Dubbo 中有哪些负载均衡策略？

参考：[Dubbo - 负载均衡](obsidian://open?vault=%E7%AC%94%E8%AE%B0&file=src%2Funarchived%2FDubbo%20-%20%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1)

- 轮询：平均分布；
- 随机：
- 最少活跃：解决慢提供者接收更少的请求；
- 一致性 Hash

## Dubbo 和 Spring Cloud 有什么哪些区别?

Dubbo 是基于 Netty 基于 TCP 建立的通信协议，配合 Hession 进行序列化。

Spring Cloud 则是基于 HTTP 协议的 Rest 通信协议。相对来说：
- **HTTP 会有更大的报文**（至少在 HTTP/1.0、HTTP/1.1 中）；
- 但是 HTTP 会**比 RPC 更加灵活**，不存在代码级别的强依赖；

## 参考资料

> https://juejin.cn/post/6882522177485144072