---
{"dg-publish":true,"dg-permalink":"COOK 机制","permalink":"/COOK 机制/","tags":["项目"]}
---


## 背景

展现架构迁移到 Nginx 后，为**减少首屏**浏览器的**渲染**时间，采用[[src/计算机基础/HTTP 常见问题#什么是 Chunked 机制？\|Chunked 机制]]分块传输搜索页面（header + body）。

定义：
- COOK 包：vui -> nginx
- chunked 包：nginx -> client

### 为什么需要定义 COOK 协议?

- nginx 请求 vui 时，会根据 http 状态码进行重查
- 但是当 vui 请求 US 失败时，此时页面的头部已经返回，导致 bff 无法获取真实的状态（因为此时 Response Header 已经返回，**状态码无法变更**）
- 所以需要一个协议来处理包**乱序、超时、后端故障**等问题

### COOK 协议的实现？

COOK 包：

```html
<!--COOK;SEQ:0;NEXT:1;LEN:10;STATUS:0;-->
...
<!--CK119097275-->
```