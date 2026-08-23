# oMLX：面向编码 Agent 的 Apple Silicon 推理服务器
- 来源: https://github.com/jundot/omlx
- 日期: 2026-08-23
- 标签: #MLX #Apple-Silicon #本地推理 #AI-Agent

## 摘要
oMLX 是基于 MLX 的 Apple Silicon 本地推理服务器，核心创新是分页 SSD 缓存（tiered KV cache），解决编码 Agent 频繁请求导致前缀漂移、KV cache 全量失效重算的痛点。8 月长上下文基准评测中被评为 Apple Silicon 最佳长上下文推理引擎。

## 要点
- 从 vllm-mlx v0.1.0 演进，新增 SSD 分层缓存 + 连续批处理 + VLM + macOS 菜单栏 App（PyObjC）
- 核心痛点：Claude Code/Cursor 等编码 Agent 数十次请求前缀漂移，旧服务器每次重算全量上下文
- 支持多模型并行（LLM+embedding+reranker）、OpenAI/Anthropic 双 API，可直接替换云后端
- 2026-08 评测（M5 64GB）：context >2K 后预填/TTFT 领先 dflash-mlx、Rapid MLX、MTPLX 等
