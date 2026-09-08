# MCP 2026-07-28 规范：无状态协议核心落地
- 来源: https://blog.modelcontextprotocol.io/posts/2026-07-28
- 日期: 2026-09-08
- 标签: #AI-Agent #MCP #协议标准 #无状态

## 摘要
MCP 发布史上最大改版：从双向有状态协议转为 request/response 无状态协议，移除 initialize 握手与 Mcp-Session-Id，任何请求可落到任意实例(round-robin LB 即可，无需共享存储)，换取更好的可靠性与扩展性。

## 要点
- 请求自描述(携带 _meta 协议版本/身份/能力)，新增可选 server/discover RPC；MRTR 取代需长连接的 server 主动调用(sampling/elicitation)
- 头路由(Mcp-Method/Mcp-Name)、列表结果可缓存(ttlMs/cacheScope)、授权硬化(RFC 9207 iss 校验，堵掉授权服务器混用漏洞)
- Tasks 正式加入扩展框架；SDK 月下载近 5 亿，Python/TS 累计破 10 亿
- 关联 [[AI-Agent-2026格局]]
