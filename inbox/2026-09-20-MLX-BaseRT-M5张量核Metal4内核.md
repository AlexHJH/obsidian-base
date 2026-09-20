# BaseRT：手写 Metal 4 张量核内核，M5 上大幅超过 llama.cpp 与 MLX
- 来源: https://arxiv.org/abs/2607.19438
- 日期: 2026-07-21
- 标签: #MLX #AppleSilicon #M5 #Metal #LLM推理

## 摘要
论文 BaseRT（arXiv 2607.19438，2026-07-21 提交）指出 M5 的 GPU 每个核心都内置了 Neural Accelerator——通过 Metal 4 tensor API 暴露的片上矩阵单元，是 Apple Silicon 对标独立 GPU tensor core 的硬件。作者用一套无框架的原生 Metal 运行时 + 手写 Metal 4 张量核内核（dense/MoE GEMM、flash-attention prefill），把计算密集的矩阵乘法路由到这些单元，而把访存受限的 decode 路径留给原有专用内核。

## 要点
- 实测（Apple M5 Pro，15 组配置，Qwen3 / Qwen3.5/3.6 / Llama 3.2 / Gemma 4，<1B 到 35B）：prefill 吞吐最高达 llama.cpp 的 6.4×、MLX 的 3.9×；decode 最高为 llama.cpp 的 1.75×、MLX 的 1.33×。
- 优势在 MoE 模型上最大——因为 MoE 以矩阵乘法为主，正好是张量核的射程。
- 关键陷阱：M5 仍宣称 `MTLGPUFamily.apple9`，无法通过 capability flag 探测到这些单元，只能走新的 Metal 4 tensor API。这解释了为什么主流运行时（依赖通用 Metal 路径）没能自动吃到这份算力。
- 结论与 [[MLX生态2026]] 的判断互补：M5 上 token 生成仍受内存带宽约束，但 prompt processing 的瓶颈是算力——张量核是解锁 prefill 的决定性杠杆。
- 运行时选型提示：MLX 在 <14B 模型上领先 llama.cpp 20–87%，27B 以上两者趋同（带宽成为瓶颈）；BaseRT 则在 prefill 维度另开一档。

## 关联
[[MLX生态2026]] [[推理引擎格局-vLLM-SGLang]]
