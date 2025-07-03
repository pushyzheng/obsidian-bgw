---
{"dg-publish":true,"dg-permalink":"MCP","permalink":"/MCP/"}
---


MCP（*Model Context Protocol*） 是一个开放协议，它规范了应用程序向 LLM 提供上下文的方式。MCP 就像 AI 应用程序的 USB-C 端口一样。

正如 USB-C 提供了一种标准化的方式将您的设备连接到各种外围设备和配件一样，MCP 也提供了一种标准化的方式将 AI 模型连接到不同的数据源和工具。

![Pasted image 20250703152408.png](/img/user/attachments/images/Pasted%20image%2020250703152408.png)

组成：
- MCP Hosts：像 Claude Desktop、IDE 或 AI 工具这样的程序，需要通过 MCP 访问数据
- MCP Clients：与服务器保持 1:1 连接的协议客户端
- MCP Servers：轻量级程序，每个程序都通过标准化模型上下文协议公开特定功能