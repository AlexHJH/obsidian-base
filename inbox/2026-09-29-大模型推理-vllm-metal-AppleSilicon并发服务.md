# vllm-metal v0.28.0：把 vLLM 的调度器与 paged KV 搬到 Apple Silicon
- 来源: https://vllm.ai/blog/2026-09-22-vllm-metal-v0-28-0 , https://www.docker.com/blog/docker-model-runner-vllm-metal-macos
- 日期: 2026-09-22
- 标签: #大模型推理 #vLLM #Apple-Silicon #MLX #KV-Cache

## 摘要
vLLM 官方博客发布 vllm-metal v0.28.0：把 vLLM 的 V1 调度器、paged KV 块管理、chunked prefill、采样与 OpenAI 兼容服务端带到 Apple Silicon，模型实现由 mlx-lm 提供、执行由 MLX/Metal 承担；Docker Model Runner 同步支持，Mac 上可用同一套 vLLM（含 Anthropic 兼容）API。

## 要点
- 要解决的问题从「模型能不能跑」变成「多请求重叠时的服务问题」：TTFT、内存增长与准入控制
- 内存护栏：`--gpu-memory-utilization` 设推理预算，启动时 warmup 预扣权重/激活/临时缓冲，余量给固定 KV cache；另给 MLX 可复用缓冲设上限，防止服务期间内存持续爬升（与 Ollama mlxrunner 修 KV buffer 释放同向）
- 利用 Apple Silicon 统一内存做零拷贝张量操作，配合 paged attention 与 GQA，长序列少浪费显存
- 边界（第三方论文 arXiv 2601.19139 亦以此为差异点）：不支持多模态与视觉缓存；上游 vLLM 仍是 Linux-only，Mac 侧靠插件
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
