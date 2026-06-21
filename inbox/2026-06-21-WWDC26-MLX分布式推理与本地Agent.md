# WWDC26：MLX 支持多机分布式推理与训练，本地 Agent 工作流落地
- 来源: https://developer.apple.com/videos/play/wwdc2026/233
- 日期: 2026-06-21
- 标签: #MLX #AppleSilicon #分布式推理 #本地Agent #WWDC26

## 摘要
WWDC26（2026-06-08）发布两个核心 MLX session，将 MLX 从单机推理扩展到多机分布式推理与训练，并展示在 Mac 上运行本地 AI Agent 工作流（隐私、低延迟、离线）。官方定位"几台 Mac 即可替代昂贵云基础设施"应对高负载 AI 任务。

## 要点
- Session 233「分布式推理与训练」：跨多台 Mac 扩展负载，覆盖互联效率、大模型推理、请求批处理、分布式微调、模型并行策略
- 分布式通信后端对开发者开放，提供 CLI / Python / Swift / C++ 四套 API
- Session 232「本地 Agentic AI」：OpenCode 等代码 Agent 集成 Xcode，多机扩展，全程设备端（播放 36 万+）
- 生态信号：Ollama 3 月已切 MLX 默认后端，mlxcel(Rust) v0.1.0 于 5 月发布，Apple 官方背书分布式路线
