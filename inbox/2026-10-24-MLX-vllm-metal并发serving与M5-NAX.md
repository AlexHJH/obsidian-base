# vllm-metal 0.28：Apple Silicon 上做并发 serving，M5 NAX 张量单元开始吃 prefill
- 来源: https://vllm.ai/blog/2026-09-22-vllm-metal-v0-28-0
- 日期: 2026-09-22
- 标签: #MLX #vLLM #AppleSilicon #推理引擎

## 摘要
vllm-metal 是 vLLM 官方社区维护的 Apple Silicon 硬件插件，以 MLX 为主计算后端、把 MLX 与 PyTorch 统一到同一条 lowering 路径。0.28 主打并发 serving；0.2.0（2026-04）起统一 paged varlen Metal kernel 成为默认 attention 后端，官方口径 TTFT 83×、吞吐 3.6×（对比 0.1.0）。

## 要点
- 2026-08 起用 M5 的 NAX 张量单元加速 MHA/GQA/MQA 的 prefill，这是 Metal 侧第一次真正吃到矩阵硬件
- 已内置的能力集：continuous batching、动态调度、paged KV cache、flash attention、prefix caching — 对标服务端引擎而非单机 runner
- 跨引擎基准覆盖 M1 Pro 32GB / M1 Max 64GB / M2 Max 64GB / M5 Pro 64GB，在并发 1 与 8 两档测 TTFT 与输出吞吐
- 社区评价（r/LocalLLaMA）：最接近「完整推理优化栈」的 Apple Silicon 实现，但 prefix caching 与投机解码仍不能同时拿下
- 现实建议：不要再为每个缺失能力开新 fork，应当把单个栈做对再上游进 mlx-lm / vLLM
- 同期另一支 vllm-mlx 走连续批处理，M4 Max 小模型可到约 525 tok/s
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]、[[Nvidia-GPU微架构演进]]
