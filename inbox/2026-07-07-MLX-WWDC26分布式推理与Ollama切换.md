# WWDC26：MLX 分布式推理 + Ollama 切换 MLX 引擎
- 来源: https://developer.apple.com/videos/play/wwdc2026/233, https://yage.ai/share/mlx-apple-silicon-en-20260331.html
- 日期: 2026-07-07
- 标签: #MLX #Apple-Silicon #WWDC26 #Ollama #分布式推理

## 摘要
WWDC26 设 MLX 专题，覆盖分布式推理训练 + Mac 端本地 Agentic AI。Ollama 于 2026 年 3 月宣布切换 MLX 为 Apple Silicon 默认推理引擎（预览版），标志着 MLX 从实验框架走向生产级部署的关键里程碑。

## 要点
- WWDC26 两场 MLX 专题：分布式推理训练、Mac 本地 Agentic AI，MLX 成为 Apple 生态官方 AI 运行时
- Ollama 切换 MLX 为默认引擎，Apple Silicon 推理栈从 llama.cpp 迁移到 MLX 统一后端
- mlxcel：Rust 原生 MLX 推理引擎 v0.1.0（5 月发布），调用 MLX C++ runtime，提供更轻量部署选项
- M5 芯片 + MLX + EXO 分布式方案实测，本地多 Mac 集群推理性能大幅提升
- 关联 [[MLX生态2026]]
