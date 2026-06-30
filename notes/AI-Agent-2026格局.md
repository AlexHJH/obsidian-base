# AI Agent 2026 格局

- 整理自: [[inbox/2026-06-13-ClaudeCode-AI编程拐点]], [[inbox/2026-06-13-AI-Agent框架]], [[inbox/2026-06-13-MCP与Agent范式]], [[inbox/2026-06-17-AI-Agent框架2026横评]], [[inbox/2026-06-22-Agent框架2026生产级横评]]
 整理自: [[inbox/2026-06-13-ClaudeCode-AI编程拐点]], [[inbox/2026-06-13-AI-Agent框架]], [[inbox/2026-06-13-MCP与Agent范式]], [[inbox/2026-06-17-AI-Agent框架2026横评]], [[inbox/2026-06-22-Agent框架2026生产级横评]], [[inbox/2026-06-23-Agent协议生态2026-MCP-A2A-ACP-UCP]]
- 标签: #AI-Agent #MCP #LangGraph #ClaudeCode #框架对比

## 范式跃迁
2026 年 Agent 从"被动响应"转向"主动决策"，三大驱动力：MCP 协议标准化、长期记忆架构、多 Agent 协作。

## 框架横评
- **LangGraph #1**：复杂有状态工作流首选，PostgresSaver 检查点成熟，月搜索量 27.1K 领先
- **Claude Agent SDK #2**：Anthropic 官方 SDK，编程场景最强（Claude Code 底座）
- **AutoGen 2.0**：事件驱动重构，原型验证最快，但无限对话循环风险、治理靠补
- **CrewAI 0.95**：角色→任务→流程，业务团队上手最快
- 选型铁律：验证想法→AutoGen；业务落地→CrewAI；进生产→LangGraph

## 企业落地调研（Anthropic / Material, 2026-06）
- 调研美国 500+ 技术领导者：80% 组织已获可衡量 ROI，88% 预期回报持续增长
- 57% 已在多阶段工作流部署 Agent，16% 推进到跨职能流程
- 2026 年 81% 计划更复杂场景（39% 多步骤 / 29% 跨职能项目）
- 高价值用例：数据分析与报表 60% + 内部流程自动化 48%
- 三大卡点：系统集成 46%、数据质量 42%、实施成本 43%
- Anthropic 经济指数（350万+ Claude 对话）：77% 商业 API 呈自动化模式

## MCP 协议
- 基于 JSON-RPC 双向通信，统一 Claude/GPT/DeepSeek 工具调用
- 一次开发多模型复用，成为事实标准

## Agent 协议生态 2026：四大协议分层
整理自 [[inbox/2026-06-23-Agent协议生态2026-MCP-A2A-ACP-UCP]]（2026-06-26 归档）
截至 2026 Q1，Agent 互操作已形成四大协议并存、分层互补格局（非竞争）：
- **MCP**（Anthropic 2024.11）：工具层，Agent↔工具 JSON-RPC 标准化接口，事实标准
- **A2A**（Google 2025.04）：协作层，Agent Card 数字名片 + 任务发现/委托，跨厂商协作；2026 升级支持 Agent Marketplace 直接售卖；Google Cloud Next 2026 披露已从 50+ 扩展到 **150 组织进入生产**（非试点），在不同平台 Agent 间路由真实任务（整理自 [[inbox/2026-06-26-Agent-A2A进入生产规模]]）
- **ACP**（IBM/BeeAI）：运行时层，Agent 生命周期管理与编排
- **UCP**：上下文层，统一多 Agent 共享上下文一致性
- 协议栈：MCP(工具) → A2A(协作) → ACP(运行时) → UCP(上下文)

## 记忆架构
工作记忆 → 短期记忆 → 长期记忆，向量数据库 + 知识图谱混合

## Claude Code 拐点信号
- coding agent 从 copilot → autonomous 跃迁
- Agent 推理消耗 token 量远超训练，利好推理基础设施
- 软件工程经济模型重写：按 token vs 按开发者时间计费

## 2026 生产级框架横评（2026-06）
整理自 [[inbox/2026-06-22-Agent框架2026生产级横评]]
- 基于 healthcare/logistics/fintech/电商真实生产经验横评 8 大框架
- 生产可靠性：LangGraph 与 Intuz 评级最高（5 星）；成本风险：AutoGen 2.0 高（无界对话循环）
- 选型取决于容错能力、可观测性需求、团队调试能力三点
- Jetbrains 6 月综述补充：Haystack(生产 RAG)、AutoGen(多智能体)、Semantic Kernel(企业集成)
- 与上方 [[#框架横评]]（06-17）互补：从社区热度转向生产可靠性视角
