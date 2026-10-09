# 2026 Agent 框架格局：Pydantic AI 崛起，框架走向「被支持的运行时」
- 来源: https://uvik.net/blog/agentic-ai-frameworks ; https://www.ampcome.com/post/top-7-ai-agent-frameworks-in-2025
- 日期: 2026-10-09
- 标签: #AI-Agent #框架 #LangGraph #PydanticAI

## 摘要
生产级 agent 框架形成五强格局：**LangGraph**（有状态/合规工作流默认，强在确定性与可审计）、**CrewAI**（多 agent 快速原型）、**OpenAI Agents SDK**（轻量、模型无关，experimental Swarm 的产物化）、**Google ADK**（多模态/GCP 原生）、**Microsoft Agent Framework**（AutoGen + Semantic Kernel 合流，面向 .NET/Azure）。真正的 breakout 是 **Pydantic AI**——用所有其他框架都在包裹的那层校验做底座。

## 要点
- OpenAI Agents SDK 只给 Agents / Handoffs / Guardrails / Sessions 几个原语，2025 年初发布、其后并入 AgentKit；微软 Agent Framework 1.0 已 GA
- 选型主线就一句：要不要「自己掌握控制流」——LangGraph 强在审计与人审步骤，OpenAI SDK 强在上手速度但可观测性偏弱
- Pydantic AI 卖点：用 Pydantic 校验层直接做 agent，而非在它外面再套一层 wrapper（该层已被 OpenAI SDK / ADK / Anthropic SDK / LangChain / CrewAI 依赖）
- 趋势：框架正从「编排库」演化为「被支持的运行时」，与本库 agent 运行时主线一致
- 关联 [[AI-Agent-2026格局]]、[[MLSys2026论文精选]]
