---
{"dg-publish":true,"dg-permalink":"agency-all 实现细节","permalink":"/agency-all 实现细节/","tags":["项目"]}
---


#项目 

# 背景

为了提升去哪儿代理商产量占比，提高供应链的优势，公司成立代理商团队。核心目标在于提升除携程直采、去哪儿旗舰店的间夜占比。

围绕“进订”解决代理商缓存不准、进订失败率高问题，从而提升代理商的间夜量。

### 进订失败率高问题？

![](/img/user/attachments/images/Pasted image 20220402142226.png)
- **缓存不够新鲜**：缓存有效时间过长，且接收到变价消息**不会及时刷新报价**
- **请求代理商 QPS 较大**：报价中心的变价是**以酒店维度记录**，不关心日期范围

# 业务设计

- 接管 SPA 向代理商的请求流量：由 agency 提供所有代理商的报价数据
- 降低报价中心的缓存时长：由 agency 管理缓存，来减少请求代理商流量和报价获取的时长
- 由 agency 系统来监听代理商的变价事件：**主动刷新**缓存报价，保证报价的新鲜度

![](/img/user/attachments/images/Pasted image 20220402142423.png)

# 系统模块设计

![agency-system.png|500](/img/user/attachments/images/agency-system.png)

- 实时获取报价和离线同步任务做**资源隔离**，防止相互影响。
- 抽象出缓存层，负责缓存的查询和穿透，以及更新数据，**对外层透明**。

# 功能实现

## 全链路异步化

[[src/项目经历/qunar/agency 全链路异步化\|agency 全链路异步化]]

## 熔断降级

熔断限流框架使用 *Resilience4j*。

轻量级的，支持 Java8 函数式编程的熔断、限流、重试框架，能很好地结合 Reactor 框架。

![8b1ba5bf-f8e2-448b-a4a1-d7a74097a67f.png|400](/img/user/attachments/images/8b1ba5bf-f8e2-448b-a4a1-d7a74097a67f.png)


```yaml
dynamic: 
  default: 
    circuitBreaker: 
      failureRateThreshold: 81             # 失败百分比阈值
      permittedCallsInHalfOpenState: 11    # 在半开状态下
      slidingWindowSize: 1000              # 滑动窗口的大小
      minimumNumberOfCalls: 1000     
      waitDurationInOpenState: 60          # 从 OPEN 到 HALF_OPEN 所需时间
```

降级：直接返回空报价。

## 动态线程池管理

### 为什么需要动态线程池管理?

1. 线程计算公式无法保证准确适用于实际业务，上线后需要调整；
2. **分环境配置**：业务场景下，会将相同代码部署在不同唤醒，分流到不同业务（或一个 Jar 包被两个 Web 应用依赖）

### 具体实现

各组件：

![](/img/user/attachments/images/agency-system-dynamic-executor-1.png)

说明：
- *DynamicExecutorRegistry*：线程池注册中心，监听 QConfig 的变化事件，执行动态调整
- *DynamicExecutorFactory*：动态线程池的创建工厂，完成线程池的初始化以及注册
- *DynamicExecutorMonitor*：线程池监控器，通过 Watcher 和日志来记录

UML 图：

![](/img/user/attachments/images/DynamicExecutorMonitor-1.png)