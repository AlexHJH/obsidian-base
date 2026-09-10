# BaseRT：用 M5 神经加速器把端侧 LLM 推理推到新天花板
- 来源: https://arxiv.org/abs/2607.19438
- 日期: 2026-09-10
- 标签: #MLX #Apple-Silicon #M5 #推理 #Metal

## 摘要
BaseRT 是绕过框架、直接用 Metal 4 写的 LLM 推理运行时。它把手写 tensor-core 内核（dense/MoE GEMM + flash-attention prefill）路由到 M5 GPU 每个核心内置的 Neural Accelerator，计算密集的 prefill 走 tensor core、带宽受限的 decode 留在原专用内核，在 M5 Pro 上同时超过 llama.cpp 与 MLX。

## 要点
- prefill 吞吐最高 6.4x vs llama.cpp、3.9x vs MLX（15 个模型配置：Qwen3/3.5/3.6、Llama 3.2、Gemma 4，参数 <1B–35B，MoE 模型优势最大）
- decode 仍保持领先：1.75x vs llama.cpp、1.33x vs MLX
- M5 仍报 MTLGPUFamily.apple9，加速器不靠 capability flag 暴露，只能通过 Metal 4 tensor API 触达——是 prefill 性能的决定性杠杆
- 开源: https://github.com/basecompute/baseRT
- 关联: [[MLX生态2026]]
