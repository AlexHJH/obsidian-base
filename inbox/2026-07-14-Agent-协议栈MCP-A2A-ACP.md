# Agent 协议栈 2026：MCP/A2A/ACP 三足鼎立
- 来源: https://zylos.ai/research/2026-02-15-agent-to-agent-communication-protocols
- 日期: 2026-07-14
- 标签: #AI-Agent #MCP #A2A #协议标准化 #多Agent

## 摘要
2026年 AI Agent 通信协议从各自为战走向互补共存。MCP (Anthropic, 已捐给 Linux Foundation) 负责 Agent↔工具连接，A2A (Google, 50+ 合作伙伴) 负责 Agent↔Agent 协作，ACP (IBM) 提供代理注册发现。三者类似 HTTP/WebSocket/gRPC 的分层共存。

## 要点
- MCP：Agent 连接外部工具/数据的"USB-C"，JSON-RPC 2.0 over HTTPS
- A2A：多 Agent 协作协议，支持 Agent Card 发现、Task 生命周期管理
- ACP：IBM 代理式架构，含 Agent Client/Server/Agent 三角色
- 行业共识：多协议共存，非互斥替代
- [[AI-Agent-2026格局]] [[2026-07-11-Agent-Q2框架排名]]
