# LM Studio mlx-engine v1.8.5：KV cache 落盘 + VLM 连续批处理
- 来源: https://lmstudio.ai/blog/mlx-engine-agentic-workloads ; https://alphasignal.ai/news/lm-studio-ships-bionic-a-full-ai-agent-built-on-open-local-models
- 日期: 2026-06-05
- 标签: #MLX #端侧推理 #KV缓存 #Agent

## 摘要
LM Studio 随其 Bionic agent 一起发布 mlx-engine v1.8.5，核心是 prompt/KV cache 检查点到磁盘（文中称 KV cache rewinding）——后续请求直接恢复缓存、不重算前缀；同时给 VLM 请求加了连续批处理。这一版针对的正是「agent 反复喂超长上下文」的负载。

## 要点
- 厂商自测（M3 Max 36GB，`Qwen3.6-27B-MLX-4bit`）：四路并行 chat 端到端 ≈2.2× 加速（输出 token 数基本一致）；长上下文 agentic 工作流 RAM 占用降 ≈82%，靠磁盘换内存
- 首次请求不省：图片 prompt 首跑 30.6s→28.6s（1.1×），只有第二次命中缓存才 23.8s→6.9s（缓存 3,584 tokens）
- 含义：agent 负载的瓶颈是「重复处理超长 prompt」而非 decode；与 WWDC26 里 Neural Accelerator 把预处理缩到 1/4 是同一问题的两条解法——硬件加速 vs 缓存复用，工程上缓存复用更便宜
- 代价与口径：缓存落盘带来写放大与本地隐私面扩大（磁盘上多一份上下文）；数字为厂商单机自测，未第三方复核
- 关联 [[MLX生态2026]]、[[AI-Agent-2026格局]]、[[推理引擎格局-vLLM-SGLang]]
