# BaseRT: Apple Silicon 上最快的 LLM 推理运行时
- 来源: https://arxiv.org/abs/2607.00501
- 日期: 2026-07-01
- 标签: #MLX #Apple-Silicon #LLM推理 #Metal

## 摘要
BaseRT 是纯 C++ 实现的原生 Metal 推理运行时，零依赖 MLX/PyTorch/CoreML。在 M4 Pro 上实测 decode 速度比 llama.cpp 快 1.56x、比 MLX 快 1.35x，MoE 模型 prefill 快 1.81x，为 Apple Silicon LLM 推理建立新 SOTA。

## 要点
- 直接基于 Metal API，针对统一内存拓扑做 chip-specific kernel fusion 和零分配 decode loop
- 支持 Qwen3、Llama 3.2、Gemma 4 系列，覆盖 Q2-FP16 八种量化格式
- 在 M3/M4 Pro 上全面超越 llama.cpp 和 MLX 两大主流方案
- 开源发布在 HuggingFace，已引发社区广泛关注
