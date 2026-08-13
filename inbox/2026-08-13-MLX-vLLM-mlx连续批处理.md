# vLLM-mlx：Apple Silicon 原生连续批处理与多模态推理（EuroMLSys'26）
- 来源: https://arxiv.org/abs/2601.19139
- 日期: 2026-08-13
- 标签: #MLX #Apple-Silicon #推理引擎 #连续批处理 #多模态

## 摘要
EuroMLSys'26 论文（arXiv 2601.19139，Wayner Barrios）提出 vllm-mlx：基于 MLX 的原生 LLM/MLLM 推理框架，利用统一内存零拷贝把 vLLM 式连续批处理搬到 Apple Silicon。小模型在 M4 Max 最高 525 tok/s，16 并发吞吐提升 4.3×，25+ req/s 足以支撑本机多 Agent 协作。

## 要点
- 文本模型吞吐比 llama.cpp 高 21%–87%（Qwen3-0.6B 至 Nemotron-30B）；连续批处理在 M4 Max 上小模型达 525 tok/s
- 多模态：内容哈希 prefix caching 消除重复视觉编码，支持 VLM 多轮对话；区别于 vLLM-metal（混合 MLX+PyTorch 方案）
- OpenAI 兼容 API 可 drop-in 替换云服务，本地 agent swarm 免云延迟与限流
- 与 [[MLX生态2026]] 衔接：Apple Silicon 从「单机跑得动」走向「并发可服务」
