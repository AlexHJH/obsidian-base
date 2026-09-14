# DeepSeek Harness：把 Agent 循环本身也做成插件
- 来源: https://github.com/deepseek-ai/deepseek-harness
- 日期: 2026-08-13
- 标签: #AI-Agent #AgentHarness #DeepSeek #开源

## 摘要
DeepSeek 8/13 开源开发者预览版 dsh（MIT，TypeScript，基于 Cordis 元框架）：模型适配、工具、技能、会话、沙箱、存储、调度乃至 agent loop 都是插件，没有需要打补丁的「特权内核」。GitHub star 一月内达 22.25 万（发布首日约 2.75 万、两天 9.54 万），为 GitHub 记录的开发者工具最快增速之一。

## 要点
- 微内核 + 一切皆插件：任一层可替换，含模型适配器；可将 Claude Code / Codex 作为子进程驱动（支持 ACP）
- 定位自托管：沙箱、凭据与审计需自备，适合把 harness 从零件拼装或用一个 harness 统辖多个 harness 的场景
- 迭代速度即风险：官方明示会持续引入兼容性破坏性变更，仓库 9/11 仍在推送，两周多即多次变动
- 生态含义：harness 从框架内置特性升级为独立层与竞争焦点（OpenAI Agents API、微软 Agent Framework Harness 同向）
- 关联 [[AI-Agent-2026格局]] [[#九款 Agent Harness 横评：换壳比换模型更能决定成败]]
