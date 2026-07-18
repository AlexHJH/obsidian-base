# 2026 AI Agent 框架格局：从百花齐放到三足鼎立
- 来源: https://www.morphllm.com/ai-agent-framework
- 日期: 2026-07-18
- 标签: #Agent #框架 #LangGraph #ClaudeAgentSDK

## 摘要
2026上半年Agent框架迎来里程碑式整合：Microsoft Agent Framework 1.0（4月3日GA）合并AutoGen+Semantic Kernel，Claude Agent SDK正式独立并计费（6月15日），Pydantic AI V2和LlamaIndex Workflows 1.0相继发布。格局从分散走向三股势力：模型厂商SDK vs 编排框架 vs 轻量工具库。

## 要点
- Microsoft Agent Framework 1.0 统一 .NET/Python，替代 AutoGen 和 Semantic Kernel
- Claude Agent SDK 以 `query()` 为入口，内置 agent loop，6月新增子 Agent 分层生成
- LangGraph 1.0 Q2 新增 per-node timeout 和 durable streaming，仍是复杂多Agent编排首选
- Pydantic AI V2 (6/23) 以 harness-first 重构，主打结构化输出和类型安全
