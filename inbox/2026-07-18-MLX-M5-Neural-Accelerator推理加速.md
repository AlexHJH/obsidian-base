# Apple M5 Neural Accelerator + MLX：本地 LLM 推理再提速
- 来源: https://machinelearning.apple.com/research/exploring-llms-mlx-m5
- 日期: 2026-07-18
- 标签: #MLX #Apple-Silicon #推理优化 #M5

## 摘要
Apple 发布 MLX 对 M5 芯片 Neural Accelerator 的支持，专用矩阵乘法单元使本地 LLM 推理速度显著提升。在 MBP M5 (24GB) 上实测多款 LLM，对比 M4 有明显加速，统一内存架构优势进一步放大。

## 要点
- M5 Neural Accelerator 提供专用 matmul 加速，MLX 首个利用该硬件的框架
- 实测对比 M4 同配置，推理吞吐大幅提升
- Ollama 0.19 已内置 MLX 后端，decode 速度从 58→112 tok/s（M5 Max + Qwen3.5-35B-A3B）
- 小模型 (<14B) MLX 比 llama.cpp 快 20-87%；大模型受限于内存带宽，差距收敛
