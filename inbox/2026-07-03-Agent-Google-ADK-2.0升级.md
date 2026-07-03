# Google ADK 2.0：Agent 执行框架的重大升级
- 来源: https://github.com/google/adk-python (ADK 2.0 CHANGELOG)
- 日期: 2026-07-03
- 标签: #AI-Agent #Google-ADK #多Agent #Task-API #框架

## 摘要
Google Agent Development Kit 发布 2.0 版本，包含 Agent API、事件模型的破坏性变更。核心新增 Task API 实现结构化 Agent-to-Agent 委托，并引入 Agent/Workflow 分离架构，Agent 负责行为定义，Workflow 负责基于图的编排。

## 要点
- Task API：多轮任务委托，支持 human-in-the-loop 和嵌套工作流
- Agent vs Workflow 分离：Agent 定义指令/工具/行为，Workflow 编排图状流程
- ADK 1.28+ 可读取 ADK 2.0 会话；多语言支持（Python/TS/Go/Java）
- 2026.2 发布集成生态（GitHub/Jira/MongoDB + 5 个可观测平台含 MLflow+OpenTelemetry）
- 关联 [[AI-Agent-2026格局]] [[LangGraph-CrewAI-2026]]
