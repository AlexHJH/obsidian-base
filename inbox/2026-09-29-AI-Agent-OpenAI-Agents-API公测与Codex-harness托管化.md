# OpenAI Agents API 公测：把 Codex 的 harness 做成托管公共品
- 来源: https://openai.com/index/introducing-the-agents-api , https://valueaddvc.com/pulse/openai-agents-api-public-beta-2026 , https://zicode.com/blog/openai-agents-api-public-beta
- 日期: 2026-09-10（公测发布）
- 标签: #AIAgent #Harness #Codex #托管服务

## 摘要
OpenAI 9/10 把驱动 Codex 的那套 agent 基础设施（harness）开放为 Agents API 公测：对象模型为 Agent（模型/指令/工具/MCP server）、可选 Environment 沙箱、持久 Session 与事件流，会话内可跑代码、改文件、联网、按预定义技能产出工件、并行拆给子 agent。

## 要点
- 计费口径：除模型 token 与第三方工具外不额外收费 → harness 本身不是产品线，而是模型消耗的漏斗；对自建 harness 的团队是一份公开成本参照
- 能力清单来自其发布说明：自动上下文压缩、工具搜索、程序化工具调用、并行子 agent（并发上限可配）、环境类型可选
- 对自建方的意义：会话持久化、沙箱、可观测从「自己搭」变成「买服务」；但产品权限、验收测试与数据义务仍归调用方，不等于质量责任转移
- 与社区观察一致：Codex 与 Claude Code 在 harness 设计维度上高度收敛（循环控制、沙箱、审批门控、可观测、上下文预算），差别在产品化程度
- 关联 [[AI-Agent-2026格局]]、[[推理引擎格局-vLLM-SGLang]]
