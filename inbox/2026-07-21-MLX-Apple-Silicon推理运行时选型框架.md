# Apple Silicon LLM 推理运行时选型：超越基准的决策框架
- 来源: https://medium.com/@michael.hannecke/choosing-an-on-device-llm-runtime-on-apple-silicon-a-decision-framework-beyond-benchmarks-2449067b8b67
- 日期: 2026-07-21
- 标签: #MLX #Apple-Silicon #推理 #llamacpp #Ollama

## 摘要
提出六维度结构化选型框架（分发模型/模型生态/集成深度/微调需求/CPU-GPU分层/供应商风险），MLX 在 14B 以下领先 20-87%，27B 以上因带宽瓶颈与 llama.cpp 趋同。建议用薄推理接口抽象屏蔽运行时变更。

## 要点
- Ollama 0.19 切 MLX 后端后，M5 Max 实测 prefill 1154→1810 tok/s，decode 58→112 tok/s
- 14B 以下 MLX 显著领先，27B+ 内存带宽成为均等瓶颈
- 18 个月内三次运行时迁移，引擎抽象不是过度设计而是架构默认项
