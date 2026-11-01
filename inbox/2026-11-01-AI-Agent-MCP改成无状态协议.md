# MCP 2026-07-28：协议改成无状态，会话与握手被删除
- 来源: https://blog.modelcontextprotocol.io/posts/2026-07-28
- 日期: 2026-07-28
- 标签: #MCP #AIAgent #协议 #无状态

## 摘要
MCP 做出自发布以来最大一次改写：核心协议改为无状态，`initialize`/`initialized` 握手与 `Mcp-Session-Id` 全部退场，任何请求可落到任何服务器；同时把 extensions 升为一等概念，并引入 12 个月最小窗口的正式弃用策略。对 agent 基础设施的意义是远程 MCP server 可以退化成普通无状态 HTTP 服务。

## 要点
- 协议版本、client 身份与 capabilities 改为逐请求放在 `_meta` 里；需要预知能力时用新的 `server/discover` RPC，但非必需（SEP-2575 / SEP-2567）
- 交互式工具改用 Multi Round-Trip Requests，不再依赖长连接会话——多轮交互从"会话状态"变成"请求内往返"
- 授权硬化：引入 RFC 9207 issuer 校验，正式从 DCR（动态客户端注册）转向 CIMD 客户端元数据文档
- 弃用清单：Roots、Sampling、Logging、DCR、legacy HTTP+SSE；features 统一分 Active/Deprecated/Removed，弃用后至少保留 12 个月
- 工程影响：Cloudflare 侧已能单 Worker 跑 MCP server；但客户端要重写原本缓存握手结果的状态机，Tier 1 SDK 已随 spec 一起发版
