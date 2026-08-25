# MiniMax-H3 全模态模型移植 MLX：Apple Silicon 本地生成视频
- 来源: https://simonwillison.net/2026/Aug/4/minimax-h3-mlx
- 日期: 2026-08-25
- 标签: #MLX #Apple-Silicon #全模态 #视频生成

## 摘要
社区项目 PipeNetwork/minimax-h3-mlx 将 MiniMax 全模态生成系统 MiniMax-H3 移植到 MLX，Apple Silicon 可本地完成「文本/图像/音频/视频 → 带音频 15 秒视频」生成，标志 MLX 生态从 LLM 文本推理扩展到视频生成新边界。

## 要点
- Simon Willison 在 M5 Max MacBook Pro 实测：约 115GB 模型下载，单次生成约 45 分钟
- 借助 mlx-vlm 处理视觉-语言部分，下载 MiniMaxAI/MiniMax-H3 与 8bit MLX 量化权重即可运行
- MLX 覆盖能力持续外扩：文本 LLM → 多模态 VLM → 视频生成，呼应「MLX 从框架升级为生态」趋势
