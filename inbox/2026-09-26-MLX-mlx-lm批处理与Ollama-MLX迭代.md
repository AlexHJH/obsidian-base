# mlx-lm 服务端批处理大更新 + Ollama MLX runner 修投机解码内存
- 来源: https://x.com/berryxia/status/2041828745569513906 , https://releasebot.io/updates/ollama , https://github.com/ml-explore/mlx-lm
- 日期: 2026-09-25（Ollama v0.34.4 发布日）
- 标签: #MLX #Ollama #Apple-Silicon #推测解码 #Agent

## 摘要
mlx-lm 放出「服务端批处理大幅提升 + 原生支持 Gemma 4」的新版本，社区实测单台 M3 Ultra 可同时跑 5 个 OpenCode 会话、130k token 预填约 1.5 分钟；Ollama 侧 v0.34.2–v0.34.4 连续修 MLX runner 的投机解码内存与批处理细节，方向一致：让 Mac 能当多 Agent 的常驻服务端。

## 要点
- Ollama v0.34.2-rc2：mlxrunner 在投机解码中按 256-token 边界释放已废弃的 KV buffer，抑制长生成时内存持续增长（原先长文生成会一路涨）
- Ollama v0.34.4（9/25，14 提交 / 70 文件）：思考模型的结构化输出改为单次流程完成；MLX 侧动态选择 Gemma 4 图像分辨率、加快 Qwen3.8 预填，XGrammar 升到 0.2.7
- mlx-lm 主线（#996–#1004 一批）：server 新增 `--allowed-origins`，Qwen3-coder 工具解析对畸形 JSON 回退 `ast.literal_eval`，补 Nemotron super 支持与 delta net 精度修复
- 与既有判断一致：批处理能力、长上下文、投机解码是 Mac 本地「多 Agent 并发」的三条硬指标，见 [[MLX生态2026#原生 MTP 解码与 MTPLX 生态位（2026-09-18 归档）]]
- 注意口径：X/Threads 上的「快了 N 倍」为单机自测；本机复现需锁 macOS 版本与官方 metallib（呼应 Xcode 26.5 编坏 NAX 内核一役）
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]、[[AI-Agent-2026格局]]
