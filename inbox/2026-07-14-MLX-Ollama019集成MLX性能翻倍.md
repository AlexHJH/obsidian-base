# Ollama 0.19 集成 MLX：Apple Silicon 本地推理性能翻倍
- 来源: https://ollama.com/blog/mlx
- 日期: 2026-07-14
- 标签: #MLX #Ollama #Apple-Silicon #本地推理 #LLM

## 摘要
Ollama 0.19 (2026年3月) 在 Apple Silicon 上用 MLX 替代 llama.cpp 作为推理后端。M5 Max 跑 Qwen3.5-35B-A3B 时 decode 从 58→112 tok/s 翻倍，prefill 从 1154→1810 tok/s。所有 M1-M5 设备均受益于 MLX 的图编译和统一内存架构。

## 要点
- MLX 比 llama.cpp 快 15-40%，内存占用低 ~10%
- NVFP4 量化首次进入 Ollama 生态，质量接近云端推理
- M5 Neural Accelerators 进一步加速 matmul，M1 用户也能获得 ~7x decode 提升
- 建议：Mac ≥32GB 用 MLX 后端，≤24GB 仍可用 llama.cpp
- [[MLX-推理优化]] [[2026-07-13-MLX-BaseRT最快推理运行时]]
