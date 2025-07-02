---
{"dg-publish":true,"dg-permalink":"jstack 分析经历","permalink":"/jstack 分析经历/"}
---


#JVM #线上问题排查 

## 起因

有些代理商反馈变化拉取的接口**长时间未调用**。

## 排查

通过观察监控后发现，发现虽然有调用，但是**间隔时间的非常长**，不合理：

![](/img/user/attachments/images/image2020-11-30_20-37-36.png)

通过排查 QSchedule 后台后发现，抓取的变价**任务执行时间特别长**：

![](/img/user/attachments/images/image2020-11-30_19-59-3.png)


查询到执行该任务的 QSchedule 线程池名称，在主机上使用 **jstack** 查看线程堆栈。

可以从栈信息中看到该线程处于 **TIME_WAITING** 挂起的状态：

![](/img/user/attachments/images/image2020-11-30_20-1-43.png)

通过堆栈信息，定义到具体的代码。发现在过滤后**没有及时地释放** CountDownLatch 锁：

![](/img/user/attachments/images/image2020-11-30_20-9-58.png)

而 CountDownLatch 共享锁的数量是抓取的供应商数量：

```java
CountDownLatch countDownLatch = new CountDownLatch(supplierGroup.size());
```