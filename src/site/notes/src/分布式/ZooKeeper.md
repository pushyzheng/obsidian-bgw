---
{"dg-publish":true,"dg-permalink":"ZooKeeper","permalink":"/ZooKeeper/"}
---


#分布式 #ZooKeeper

## ZooKeeper 是 CAP 中的哪个系统？

ZooKeeper 是 **CP**，是强一致性的，因此丢失了可用性：
1. 不能保证**每次**服务请求的可用性，可以需要重试
2. 进行 leader 选举时**集群都是不可用**