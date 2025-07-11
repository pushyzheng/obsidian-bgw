---
{"dg-publish":true,"dg-permalink":"sync.Map 实现原理","permalink":"/sync.Map 实现原理/","tags":["Go"]}
---


#Go

加锁的策略：**读写分离** + 原子操作

```go
// 内部维护了两个map
type Map struct {
	mu Mutex
	read atomic.Pointer[readOnly]   // 只读map, 存储稳定的数据, 大多数读操作无锁访问
	dirty map[any]*entry            // 脏数据map
	misses int
}

type readOnly struct {
	m       map[any]*entry
	amended bool // true if the dirty map contains some key not in m.
}
```

## Load

1. 读取时，先在 read map 中**无锁**且通过原子操作读取，如果命中则返回
2. 未命中时，**加锁**，并访问 dirty map 进行查找，并增加一个 misses 计数
3. 当 misses 值达到 dirty map 大小阈值时，会将 dirty map 的数据更新到 read map，减少后续的 miss

源码：

![Pasted image 20250709160219.png](/img/user/attachments/images/Pasted%20image%2020250709160219.png)

## Store

1. 写入操作只写入dirty map，写操作需要加锁，保证并发安全
2. 如果 dirty map 尚未初始化或脏数据太多，会触发将 dirty map 数据同步到 read map