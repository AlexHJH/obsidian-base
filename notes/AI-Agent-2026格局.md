# AI Agent 2026 格局

- 整理自: [[inbox/2026-06-13-ClaudeCode-AI编程拐点]], [[inbox/2026-06-13-AI-Agent框架]], [[inbox/2026-06-13-MCP与Agent范式]], [[inbox/2026-06-17-AI-Agent框架2026横评]], [[inbox/2026-06-22-Agent框架2026生产级横评]]
 整理自: [[inbox/2026-06-13-ClaudeCode-AI编程拐点]], [[inbox/2026-06-13-AI-Agent框架]], [[inbox/2026-06-13-MCP与Agent范式]], [[inbox/2026-06-17-AI-Agent框架2026横评]], [[inbox/2026-06-22-Agent框架2026生产级横评]], [[inbox/2026-06-23-Agent协议生态2026-MCP-A2A-ACP-UCP]]
- 标签: #AI-Agent #MCP #LangGraph #ClaudeCode #框架对比

## 范式跃迁
2026 年 Agent 从"被动响应"转向"主动决策"，三大驱动力：MCP 协议标准化、长期记忆架构、多 Agent 协作。Gartner 预测 2026 年底 40% 企业应用将集成 AI Agent。

## PE Collective 框架全景（2026-06）
整理自 [[inbox/2026-06-30-Agent-2026年Agent框架全景对比]]
- LangGraph 生产就绪度 5/5，2026.2 发布 0.3.x 引入 PostgresSaver
- AutoGen 1.0 GA（2026.2），转向 v2 事件驱动架构
- CrewAI 0.95 新增 Anthropic/Google tool-call 路由 + 异步 crew runner
- 新晋框架：Pydantic AI（类型安全）、LlamaIndex（RAG 优先）
- 成本风险：LangGraph/Anthropic 低，AutoGen 高（无界对话循环）

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

## LangGraph vs CrewAI：两种编排哲学（2026-07 归档）
整理自 [[inbox/2026-07-01-Agent-LangGraph-CrewAI生产级对决]]
- LangGraph 将有状态图作为核心抽象（`interrupt()` 一等公民支持人机协同），CrewAI 以角色-任务-流程层级建模
- 生产系统选 LangGraph（完整状态保存、可观测性强），快速原型选 CrewAI（午餐前跑通多 Agent）
- 与上方 [[#框架横评]] 互补：补充「日常开发选型心理模型」维度

## Google ADK 2.0（2026-07-03 归档）
整理自 [[inbox/2026-07-03-Agent-Google-ADK-2.0升级]], [[inbox/2026-07-07-Agent-2026多Agent框架格局与ADK2.0]]
- Google Agent Development Kit 发布 2.0，包含 Agent API、事件模型破坏性变更
- 核心新增 Task API：结构化 Agent-to-Agent 多轮任务委托，支持 human-in-the-loop 和嵌套工作流
- Agent vs Workflow 分离架构：Agent 定义指令/工具/行为，Workflow 负责基于图的编排
- 2026.2 发布集成生态（GitHub/Jira/MongoDB + 5 个观测平台含 MLflow+OpenTelemetry）
- ADK 1.28+ 可读取 ADK 2.0 会话，多语言支持 Python/TS/Go/Java
- I/O 2026 正式发布 Antigravity 编排面板 + Managed Agents 企业级部署能力
- 协议层趋同：MCP（工具连接，9700 万下载）和 A2A（Agent 通信，150 组织生产级）成为事实标准
- 六大框架月搜索量：LangGraph 27,100 领先，CrewAI 14,800 第二

## 微软 Agent Framework 1.0 GA + Anthropic SDK 独立计费（2026-07 归档）
整理自 [[inbox/2026-07-05-Agent-2026框架格局与MCP-A2A协议之争]]
- 微软 Agent Framework 1.0 GA：合并 AutoGen + Semantic Kernel，统一 .NET 生态 Agent 开发
- Anthropic Claude Agent SDK 6 月独立计费上线，引入 query() 生成器、生命周期钩子、子 Agent 隔离等新原语
- Google ADK 1.0 支持 Java/Go，拓展企业后端生态
- MCP（单 Agent 工具调用）与 A2A（多 Agent 协作）两大协议生态持续分化，Google 发布六大协议对比指南

## 2026 Q2 生产级框架排名：LangGraph 领跑，Pydantic AI V2 崛起（2026-07 归档）
整理自 [[inbox/2026-07-09-Agent-Q2框架排名]]
- Alice Labs 基于 18+ 生产部署发布 Q2 排名：LangGraph 1.0 稳居第一
- Pydantic AI V2（6/23）和 LlamaIndex Workflows 1.0（6/22）成最大黑马
- LangGraph 1.0 新特性：per-node timeouts、DeltaChannel、v2 streaming
- Claude Agent SDK（6 月）：子 Agent 层级生成 + fallback 模型链，Anthropic 原生生产 Agent
- CrewAI 1.14（5-6 月）：可插拔后端 + Chat API，多 Agent 原型最快路径
- Pydantic AI V2：harness-first 重设计，类型安全 Python Agent 新范式
- 选型速查：进生产→LangGraph；Anthropic 生态→Claude SDK；快速原型→CrewAI；类型安全→Pydantic AI

## Agent 支付协议兴起：AP2 / ACP / MPP / x402（2026-06-28 归档）
整理自 [[inbox/2026-06-28-Agent-Agent支付协议AP2-ACP-MPP]]
- 2026 年 Agent 协议栈从工具/协作层延伸至支付层，在 MCP/A2A 之上涌现四大支付协议
- AP2（Google，60+ 伙伴）：加密签名 Mandate，A2A+MCP 的支付层伴侣，支持卡 + 加密货币
- ACP（OpenAI×Stripe）：结账流标准化；MPP（Stripe×Tempo）：sessions 模型 + 流式微支付
- x402（Coinbase）：加密支付标准；Adyen Agentic（2026-06-16）：商户一次集成覆盖全 agent 商业
- 与上方 [[#Agent 协议生态 2026：四大协议分层]] 互补：从「Agent 互操作」延伸至「Agent 自主交易」

## 2026 框架格局：从百花齐放到三足鼎立（2026-07-18 归档）
整理自 [[inbox/2026-07-18-Agent-2026框架格局大 consolidation]]
- 三股势力成型：模型厂商 SDK（Claude/OpenAI/Google）vs 编排框架（LangGraph）vs 轻量工具库（Pydantic AI）
- Microsoft Agent Framework 1.0 GA（4/3）合并 AutoGen+Semantic Kernel，统一 .NET/Python
- Claude Agent SDK 6/15 独立计费，`query()` 为入口 + 子 Agent 分层生成
- Pydantic AI V2（6/23）harness-first 重构，主打结构化输出和类型安全

## Personal Agent 峰会与安全三态模型（2026-07-24 归档）
整理自 [[inbox/2026-07-20-Agent-2026框架格局与Personal-Agent峰会]]
- 7/21 Personal AI Agents Summit 汇聚 OpenAI/Google/Anthropic/Perplexity，聚焦个人 Agent 安全与生产落地
- Anthropic 提出三态安全模型：block（直接拒绝）/ allow（放行）/ triage（人工审核），每种策略过度使用均有失败模式
- OpenAI 成立 Deployment Company，派遣 Forward Deployed Engineers 嵌入客户现场加速落地
- 生产团队趋势：多框架组合——LangGraph 做编排 + AgentCore 做部署 + Strands 做轻量 Agent
- 关联 [[#Agent 协议生态]]（MCP + A2A 跨厂商标准支撑）

## 2026 H1 框架评选共识：LangGraph 登顶（2026-07-12 归档）
整理自 [[inbox/2026-07-12-Agent-框架格局2026]]
- Airbyte/Firecrawl/Let's Data Science 等多家独立评测一致确认 LangGraph 为生产最成熟框架
- LangGraph 生产可靠性最高但上手 7-14 天；CrewAI 最快 1-2 天原型验证
- MCP 成为 Agent 互操作标准，LangGraph 原生支持
- 八大框架：LangGraph / CrewAI / AutoGen 2.0 / OpenAI SDK / Anthropic SDK / Google ADK / LlamaIndex / Intuz
- 趋势：从「快速原型」全面转向「生产级可靠性与合规」

## 企业 Agent 部署现状：85% 试点仅 5% 上生产（2026-07-25 归档）
整理自 [[inbox/2026-07-22-Agent-企业部署现状与混合控制面]]
- VentureBeat 调研 101 家企业：85% 试点 Agent 但仅 5% 上生产，多数「Agent」实际是 Chatbot 包装器
- Anthropic Claude 是企业首选平台，51% 预计年底采用混合控制面（provider native + 外部编排）
- 供应商锁定是最大担忧（35%），实时 token 成本控制仍是盲区
- Amazon AGI 实验室提出「实习生」管理框架：consistency/robustness/predictability/safety 四维可靠性

## Vercel AI SDK 7 与 Q2 框架发布潮（2026-07-24 归档）
整理自 [[inbox/2026-07-24-Agent-Q2框架迭代狂潮]]
- 2026 Q2（4-7月）是 Agent 框架史上最密集迭代季：Pydantic AI V2 与 LlamaIndex Workflows 1.0 在 48 小时内先后 stable
- Vercel AI SDK 7 推出三种 Agent 抽象：ToolLoopAgent / HarnessAgent / WorkflowAgent
- MCP 2026-07-28 spec 重写为无状态协议层，进一步简化工具接入
- 关联 [[#MCP/A2A/ACP 协议栈分层共识]]

## 2026 H1 框架格局：大一统趋势（2026-07-30 归档）
整理自 [[inbox/2026-07-26-Agent-2026年中框架格局]]（Morphllm 综述）
- 2026 H1 框架进入「大一统」时代：LangGraph 巩固状态图路线，微软合并 AutoGen+SK，Anthropic 引入子代理独立计费
- LangGraph 1.0 月下载 39.2M，per-node timeouts + durable streaming
- Claude Agent SDK 6月推出 hierarchical subagent spawning，6/15 独立计费
- Microsoft Agent Framework 1.0 4/3 GA；CrewAI 1.14 可插拔后端
- Pydantic AI V2 & LlamaIndex Workflows 1.0 6月下旬同日发版

## MCP/A2A/ACP 协议栈分层共识（2026-07-14 归档）
整理自 [[inbox/2026-07-14-Agent-协议栈MCP-A2A-ACP]]
- MCP（已捐给 Linux Foundation）：Agent↔工具 "USB-C" 标准化接口，JSON-RPC 2.0 over HTTPS
- A2A（Google, 150+ 组织生产）：Agent↔Agent 协作，Agent Card 发现 + Task 生命周期管理
- ACP（IBM）：代理式架构，含 Agent Client / Server / Agent 三角色，负责注册发现
- 三者类似 HTTP/WebSocket/gRPC 的分层共存，行业共识：多协议共存，非互斥替代

## Q2 框架三门槛与企业落地五大趋势（2026-08-09 归档）
整理自 [[inbox/2026-07-30-Agent-框架Q2格局三大门槛]]、[[inbox/2026-07-28-Agent-框架Q2格局]]、[[inbox/2026-07-30-Agent-企业落地五大趋势]]
- **选型三门槛**：持久化状态+HITL 一等公民、原生 MCP、原生 A2A；MCP 规范 2026-07-28 定稿，七大主流框架全部原生支持
- Microsoft Agent Framework 1.0（4/3）合并 Semantic Kernel + AutoGen，目前唯一同时原生支持 MCP+A2A（.NET+Python）；LangGraph 1.0 per-node timeout/DeltaChannel；Google ADK 2.0 五语言 graph engine；Claude Agent SDK 层级 subagent
- **企业落地五大趋势**：岗位级部署、多 Agent 协作架构（规划→执行→验证→汇总）、AgentOps 运维刚需（监控/审计/权限/异常四件套）、个人 Agent 普及、行业垂直 Agent 爆发
- 案例：头部保险公司 Agent 理赔初审单日 2000→15000 件、准确率 97.3%、人力降 60%
- 关联 [[#2026 Q2 生产级框架排名]]、[[#2026 H1 框架格局：大一统趋势]]
