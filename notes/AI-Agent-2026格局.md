# AI Agent 2026 格局

- 整理自: [[inbox/2026-06-13-ClaudeCode-AI编程拐点]], [[inbox/2026-06-13-AI-Agent框架]], [[inbox/2026-06-13-MCP与Agent范式]]
- 标签: #AI-Agent #MCP #LangGraph #ClaudeCode #框架对比

## 范式跃迁
2026 年 Agent 从"被动响应"转向"主动决策"，三大驱动力：MCP 协议标准化、长期记忆架构、多 Agent 协作。

## 框架横评
- **LangGraph #1**：复杂有状态工作流首选，PostgresSaver 检查点成熟
- **Claude Agent SDK #2**：Anthropic 官方 SDK，编程场景最强
- **AutoGen 2.0**：事件驱动重构，但无限对话循环风险
- **CrewAI 0.95**：角色式多 Agent 协作

## MCP 协议
- 基于 JSON-RPC 双向通信，统一 Claude/GPT/DeepSeek 工具调用
- 一次开发多模型复用，成为事实标准

## 记忆架构
工作记忆 → 短期记忆 → 长期记忆，向量数据库 + 知识图谱混合

## Claude Code 拐点信号
- coding agent 从 copilot → autonomous 跃迁
- Agent 推理消耗 token 量远超训练，利好推理基础设施
- 软件工程经济模型重写：按 token vs 按开发者时间计费
