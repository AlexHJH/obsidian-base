# 阿里云 AgentCore 发布：把「多来源 Agent」收敛成一套运行与治理底座（云栖大会 2026）

- 来源: https://www.aliyun.com/product/agentcore ; https://developer.aliyun.com/article/1761911 ; https://wallstreetcn.com/articles/3782339
- 日期: 2026-09-22
- 标签: #AIAgent #企业智能体 #治理 #Harness #阿里云

## 摘要
云栖大会（9/22–24）上阿里云发布企业级 Agent 平台 AgentCore：以「构建与运行、协作与治理、观测与评估」三大能力覆盖 Agent 全生命周期，同时发布《阿里云企业级智能体手册》。核心卖点不是模型能力，而是把自研、开源 Harness 与商业 SaaS Agent 多来源纳管进同一套运行时、身份与审计体系。

## 要点
- 三种接入：Harness 配置化创建（零编码）、高代码框架原生部署（LangChain/LangGraph/Google ADK/AgentScope）、存量 Agent 零重写纳管
- 统一纳管范围覆盖三类来源：开源 harness（OpenClaw、QwenPaw）与商业 SaaS agent（Claude Code、Codex、Qoder/QoderWork）——即「harness 之上再套一层管理平面」
- 运行底座：会话级运行时沙箱强隔离、秒级弹性伸缩；模型/工具/技能/记忆/知识库（RAG 接百炼与 RAGFlow）统一资产管理
- 协作层：多人与多 Agent Team 编排，复杂任务动态拆解调度，**长周期任务可中断可恢复**，故障自动接管
- 治理与可观测：人类用户与 Agent 统一身份认证鉴权、入站出站双向管控、凭据隔离托管、全量审计日志、调用链/工具链/决策链全链路追踪（接入 CMS 2.0）
- 与 [[AI-Agent-2026格局]] 中「harness 工程模式商品化」同向：微软 MAF / OpenAI Agents API 卖 harness 本身，AgentCore 卖的是 harness 的**统一纳管与问责层**
- 对本机 [[LongLoop Console]] 的直接参照：会话沙箱隔离 + 长任务可中断可恢复 + 凭据隔离 + Token 消耗可审计，正是长任务框架的四个工程面
