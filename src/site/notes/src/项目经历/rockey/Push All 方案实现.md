---
{"dg-publish":true,"dg-permalink":"Push All 方案实现","permalink":"/Push All 方案实现/"}
---


#项目 

## 表设计

- *biz_id*：业务 ID
- *task_name*：任务的名称
- *task_id*：任务 ID
- *pod_identity* 的规则：**Pod 的 Id + Pod IP**
- *request_id* 由业务方生成，做**幂等**
- *data* 可用于存储任务的**进度数据**（version），用于重启恢复后断点执行
- *params*：请求参数
- *retries* 记录重试次数，限制**最大的重试次数**
- *status*：记录任务的状态

## 流程

### 任务的创建

可以将任务的创建抽象生产者和消费者模型：
- Producer：RPC 接口调用后，新增任务表行记录
- Consumer：从任务表中扫描最新任务数据

如下图所示：

![push-task-consumer-producer 1.png|600](/img/user/attachments/images/push-task-consumer-producer%201.png)

### 抢占任务

触发任务时：
1. 生成 Task ID 和 PodIdentity（Worker 唯一标识）
2. CAS 更新记录

![Pasted image 20220830222321.png|400](/img/user/attachments/images/Pasted%20image%2020220830222321.png)

SQL 如下：

```sql
UPDATE push_task
SET status = RUNNING, task_id = ${taskId}, pod_identity = ${podIdentity}
WHERE status = INITIAL;  -- 只更新初始状态的任务记录
```

任务被成功抢占后，会向 *TaskRegistry* **注册本地任务**，并开始执行。

```java
public interface BasePushTask {
	boolean doExecute(TaskContext context) throws Exception;
}
```

### 保活机制（KeepAlive）

> [!INFO] 
> 判断一个任务**是否死亡**？
> 
> 即根据 *updateTime* 与当前时间的间隔 gap，是否超过设置的时间（*max-valid-time*）

所以，为了进行任务的保活，会有定时任务 *KeepAliveTask* 定时轮询注册中心该 Worker 的本地任务，更新 *updateTime* 与 *Podidentity*，同时也是一次**该 Worker 是否持有该任务的一次探测**。

![Pasted image 20220830225223.png|400](/img/user/attachments/images/Pasted%20image%2020220830225223.png)

CAS 更新 SQL 如下：

```sql
UPDATE push_task
SET pod_identity = ${podIdentity}, updateTime = ${updateTime}
WHERE taskId = ${taskId};
```

处理：
- 更新成功：该 Worker 仍然持有此 Task
- 更新失败：该 Worker **已经不持有此 Task**，*TaskContext* 中的 `holdsTask` 标识会被更改，可以被本地任务感知到。

> [!NOTE] 
> 更新失败的可能会发生在原因为：
> 
> 该 Worker 由于系统原因（如 JVM 负载过高停止工作），长时间没有执行逻辑，如果持续下去任务将出现假死的情况，需要将任务转交给其他 Worker 继续执行。

### 任务延续（WatchDog）

> [!INFO] 
> 为什么要有任务的延续？
> 
> 1. 持有任务的 Worker 出现假死，需要接管
> 2. 先前的持有任务的 Worker Pod 被打断（发布 or 重启），需要重新执行
> 3. 任务执行失败退出后重试

为了延续任务：会定时从库中查询状态为 *RUNNING（执行中）、INTERRUPTED（被打断）、FAILED（执行失败）* 状态的任务记录。

并判断任务是否死亡，即状态为：
- FAILED
- INTERRUPTED
- RUNNING 且 `(now - task.getUpdateTime()) > durationThreshold`

和保活机制一样，CAS 更新 PodIdentity 和 updateTime，**如果更新成功则代表抢占成功**，继续执行。