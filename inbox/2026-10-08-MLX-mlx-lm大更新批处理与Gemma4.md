# mlx-lm 大版本：服务端批处理 + 原生 Gemma 4 + 单机多 Agent
- 来源: https://x.com/berryxia/status/2041828745569513906 ; https://ai.google.dev/gemma/docs/integrations/mlx ; https://developer.apple.com/videos/play/wwdc2026/232
- 日期: 2026（X 帖文，具体日期未核；Gemma 集成文档更新 2026-04-17）
- 标签: #MLX #AppleSilicon #Agent #Gemma #批处理

## 摘要
mlx-lm 放出大更新：服务端批处理能力大幅提升、原生支持 Gemma 4，单台 M3 Ultra 可同时跑 5 个 OpenCode 会话。这与 WWDC26 的定位一致——MLX-LM Server 用连续批处理承接「主 Agent 派生子 Agent」的并发请求，是本地 Agent 栈从「能跑」到「能当服务端」的关键一步。

## 要点
- 瓶颈已从单流 decode 转到并发调度：子 Agent 并行（读文档/搜代码/写测试）会把多个请求同时打到本地模型，批处理质量决定体验
- Gemma 4 进 mlx-community（如 `gemma-4-31b-it-bf16`、`gemma-4-e2b-it-4bit`）；Google 官方给出 `mlx_lm.generate` 与 `mlx_vlm.server` 用法，文本与视觉两条路都支持
- WWDC26 的分布式数据佐证路线：4 台 Mac 集群跑 Qwen 3.6 27B 近 3× 加速；LoRA 微调 180 → 600 tok/s；10 月新 Mac Studio 上 512GB 配置是 671B 级模型的容量档
- 版本节奏：MLX 0.32.0（2026-07-07）、MLX Swift 0.31.6 同周发布，约每几周一次 → 需锁版本，别裸追 main
- 关联 [[MLX生态2026]]、[[AI-Agent-2026格局]]
