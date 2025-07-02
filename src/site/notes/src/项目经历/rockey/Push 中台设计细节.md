---
{"dg-publish":true,"dg-permalink":"Push 中台设计细节","permalink":"/Push 中台设计细节/"}
---


#项目

## 架构

![Pasted image 20220830232257.png|600](/img/user/attachments/images/Pasted%20image%2020220830232257.png)

## 技术方案

### 分库分表

使用 Sharding JDBC 实现：
1. 支持以业务维度（BizId）分库，且同一个 Biz 下还支持分库
2. 支持一个库下分表（分表数量可配）

![Pasted image 20220830213800.png|500](/img/user/attachments/images/Pasted%20image%2020220830213800.png)

实现方式：使用 [Hint 分片算法](https://shardingsphere.apache.org/document/legacy/4.x/document/cn/features/sharding/concept/sharding/#sql-hint) 已经相应的[类扩展实现配置](http://192.168.10.15:49165/rockey/platform-push/-/blob/dev/platform-push-component/src/main/java/com/highlight/platform/push/repository/impl/PushDeviceTokenTableHintShardingAlgorithm.java#L39-63)，比较灵活地来进行分库分表。

```yaml
platform:
  push:
    biz:
      # 业务配置
      LIVEPIC:
        sharding:
          # 分库规则
          database-rule:
            actual-data-nodes: [ 'db1' ]
          # 分表规则
          table-rule:
            actual-table-number: 1
```

### 全量推送

[[src/项目经历/rockey/Push All 方案实现\|Push All 方案实现]]