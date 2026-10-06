# Rapid-MLX 0.15.4：由 vLLM-MLX 更名，主打 coding agent 的工具调用可靠性

- 来源: https://rapidmlx.com/changelog ; https://github.com/raullenchai/Rapid-MLX
- 日期: 2026-10-02
- 标签: #MLX #Apple-Silicon #推理引擎 #Agent #工具调用

## 摘要
Rapid-MLX（前身 vLLM-MLX，2026-03-13 更名，包名 rapid-mlx）在 10/2 发布 0.15.4，新增实验性加速 profile 与 Desktop 的 Share Compute。它定位为 OpenAI/Anthropic 兼容的本地推理服务 + Mac app，重点不在跑分而在工具调用可靠性，并对 Claude Code、Codex CLI、Aider、Hermes、DeepSeek Harness 做发布门禁式验证。

## 要点
- 承诺数据：较 Ollama 快 2–4×；M3 Ultra 256GB 上 122B→57 tok/s 且 100% 工具调用、35B→83 tok/s、397B/209GB 单机可跑
- 引擎特性：paged KV cache、prefix cache、连续批处理（源自 vLLM-MLX）；DeltaNet 类混合架构用 state snapshot 把多轮 TTFT 压到约 0.1ms
- 兼容层：17 个 tool parser、reasoning 分离、云路由（本地/远端混合）；Tier-1 工具链最后端到端复核 2026-07-28
- 观察：MLX 生态已从「框架 + 官方工具链」长出第三方重写版推理引擎，直接与 Ollama/llama.cpp 争夺本地 agent 的默认运行时
- 口径提示：性能与工具调用准确率均为项目自报，跨引擎对比需自行复测
- 关联 [[MLX生态2026]]、[[AI-Agent-2026格局]]
