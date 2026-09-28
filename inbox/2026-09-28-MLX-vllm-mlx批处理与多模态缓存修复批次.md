# vllm-mlx 工程修复批次：批处理 API 对齐 mlx-lm 0.31.x 与多模态缓存串味

- 来源: https://github.com/waybarrios/vllm-mlx/releases , https://arxiv.org/html/2601.19139v2
- 日期: 2026-09-28（releases 页滚动更新，逐条未标确切日期）
- 标签: #MLX #Apple-Silicon #批处理 #KV-Cache #多模态

## 摘要
把 vLLM 式连续批处理搬到 Apple Silicon 的 vllm-mlx，近期一串修复集中在两件事：跟上游 mlx-lm 的缓存 API 对齐（BatchGenerator / BatchKVCache / BatchMambaCache / ArraysCache），以及多模态前缀缓存的正确性——按内容哈希去重的「重复图 28× 加速」被查出了跨请求命中错误与高并发下 Metal 资源泄漏。

## 要点
- API 漂移是主要维护成本：需持续对齐 mlx-lm 0.31.x 的 `BatchGenerator`、0.30.5+ 的 GLM-4 支持、0.30.6+ 的 `BatchMambaCache(size)`、Qwen3.5 的 `ArraysCache` 混合模型批处理——上游一改缓存签名，服务端就编译不过或静默跑错
- 模型面补丁：Gemma 4 的 `BatchKVCache`、attention 与 `RotatingKVCache`（#268/#256）、MLLM 批处理里的 RotatingKVCache 支持
- 正确性事故（值得记住的两类）：base64 图像缓存哈希被截断 + **跨请求数据泄漏（#92）**；prompt_tokens 在 SimpleEngine 恒报 0（#236）
- 稳定性：高并发下 Metal 资源泄漏修复（#92）、调度器 trim 方法存在性检查（#152）、流式 UTF-8 安全 detokenizer（#109）
- 论文口径可对照：文本吞吐高于 llama-cpp 21%–87%、16 并发下总吞吐 4.3×，多模态重复图查询 28× 加速、延迟 21.7s → <1s（M4 Max）
- 读法：多模态「按内容哈希前缀缓存」省算力的同时把缓存键的正确性变成一等风险——本地 Mac 做多 Agent 服务端时要专门测这条路径
- 关联 [[MLX生态2026#vllm-mlx 多模态 + WWDC26 多机分布式细节（2026-09-04 归档）]]、[[推理引擎格局-vLLM-SGLang]]
