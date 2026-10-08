# vllm-mlx：纯 MLX 原生 LLM/MLLM 推理服务器，吞吐超 llama.cpp 21–87%
- 来源: https://arxiv.org/abs/2601.19139 ; https://github.com/waybarrios/vllm-mlx ; https://waybarrios.com/projects/vllm-mlx
- 日期: 2026-01-27（论文 v1，v2 2026-01-29）
- 标签: #MLX #AppleSilicon #推理服务器 #多模态 #连续批处理

## 摘要
Wayner Barrios 的 vllm-mlx 用纯 MLX（不依赖 PyTorch/MPS）在 Apple Silicon 上做 LLM 与多模态推理服务，文本模型吞吐比 llama.cpp 高 21%–87%（Qwen3 家族 1.17×–1.87×）；同时把「服务化」要素补齐——单一进程暴露 OpenAI 与 Anthropic 两套 API、连续批处理、前缀缓存、MCP tool calling。

## 要点
- 基准（论文表）：Qwen3-0.6B 525.5 vs llama.cpp 281.5 tok/s（1.87×）；Qwen3-4B 1.35×；Qwen3-8B 1.21×；Qwen3-30B-A3B 1.17×（对比对象含 vllm-metal、mlx-lm）
- 差异化在 MLLM：内容寻址的前缀缓存复用视觉编码，同一张图反复提问的延迟从约 21s 降到 <1s（vllm-metal 不支持多模态）
- 与 vLLM-Metal 分工：后者是 vLLM 官方 Apple 插件（MLX + PyTorch 混合，接 vLLM 平台抽象）；vllm-mlx 是独立纯 MLX 服务器，只对齐 API
- 工程验证：DeepSeek-V4-Flash-0731 MXFP4（283.8B）在 M3 Ultra 上 11/11 HTTP 工具调用通过，批处理下每 token 解析开销不随并发增长
- 口径提醒：21–87% 系论文作者自测，最激进的缓存收益不可外推；关联 [[MLX生态2026]]
