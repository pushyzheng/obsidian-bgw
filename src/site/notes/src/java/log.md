---
{"dg-publish":true,"dg-permalink":"log","permalink":"/log/"}
---


#Java #Java框架 

## logback 的 AsyncAppender 都有哪些参数？

- 参数：
	1. includeCallerData：是否收集调用方的数据
	2. queueSize：阻塞队列的大小
		- 过大，容易 OOM
		- 过小，容易导致数据的丢失
	3. discardingThreshold：丢弃日志的阈值（默认 80%）
	4. neverBlock：队列满时，是否阻塞（否则将会丢弃数据）
- 如何设置：
	- 性能为先：`neverBlock = true`
	- 数据不能丢失：`discardingThreshold = 0`
	- 兼顾两者：*queueSize* 大些，再设置个合理的 *discardingThreshold*