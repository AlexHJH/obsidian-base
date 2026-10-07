# Harness 概念地图：把「模型之外的一切」显式化

- 来源: https://www.developersdigest.tech/blog/what-is-an-agent-harness-2026
- 日期: 2026-10-03
- 标签: #AIAgent #Harness #运行时 #沙箱 #可观测

## 摘要
Developers Digest 把 2026 年四处开花的「harness」收成一个可操作定义：包住无状态模型、让它能干真活的一切 —— 调用它的循环、它能用的工具、它携带的上下文与记忆、它运行的权限与沙箱、以及事后可回放的日志。最宽的一端借用 Shrivu Shankar《The Harness Is the Company》的说法「所有围绕无状态 LLM 的 infra、接口、上下文与状态」；最窄的一端就是 Codex / Claude Code / OpenCode / LangGraph 这类外壳。

## 要点
- 读真实代码最能说清：DeepSeek Harness 的运行时 = 会话日志 + agent loop + 工具调度器 + 沙箱 + Web UI + SDK；Pi 自述「hardened, minimal, extensible」是另一个极端，两者同一物种
- 两周内的发布把词推成默认用语：Pi 1.0、DeepSeek 给开源 harness 配桌面应用、OpenAI DevDay 把 Codex 的循环挪到 Agents API 之后
- 核心论据：同一模型在不同 harness 下表现不同（Databricks 有度量）→ 模型能力不是唯一变量，harness 是独立工程面
- 选型读法：harness 从「框架 API」变成被运维与治理的对象；与 [[AI-Agent-2026格局]] 的「运行时干预」主线、[[LongLoop Console]] 的 harness + 治理 + 可观测三件套同向
- 口径提示：厂商/媒体侧地图文章，无独立评测数据
