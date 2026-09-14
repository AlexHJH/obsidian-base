# 九款 Agent Harness 横评：换壳比换模型更能决定成败
- 来源: https://winder.ai/ai-agent-harness-comparison
- 日期: 2026-08-20
- 标签: #AI-Agent #AgentHarness #工具选型

## 摘要
Winder 实测七款 harness 后给出 2026 年横评：harness = 除模型以外的全部代码、配置与执行逻辑，栈分四层（模型 → harness 跑单个 agent → 框架编排多 agent → 平台管理一队 harness）。九款产品的真正差异不在功能清单，而在各自假定的前提（绑哪家模型、托管还是自建、谁能改行为）。

## 要点
- harness 内部六组件已收敛（Raschka）：仓库存活上下文、缓存前缀 + 可变后缀的 prompt、带校验与路径权限的工具、裁剪压缩、独立于全量 transcript 的会话记忆、有递归上限的只读子 agent
- 换壳收益量级：Vercel 砍掉 80% 工具后成功率 80%→100%、token 减半、耗时 724s→141s；CORE-Bench 同模型换脚手架 42%→78%；LangChain 52.8%→66.5%
- 互通趋势：Zed Agent、OpenHands、DeepSeek Harness 均讲 Agent Client Protocol，可互相驱动；Linux 基金会 Agentic AI Foundation 收纳 MCP、AGENTS.md、Goose
- 选型建议：按「假定」而非 feature grid 选——两家绑定厂商（Claude Code/Codex），五家自托管（OpenCode/Qwen Code/dsh/Goose/OpenHands）
- 关联 [[AI-Agent-2026格局]] [[#DeepSeek Harness 把 Agent 循环做成插件]]
