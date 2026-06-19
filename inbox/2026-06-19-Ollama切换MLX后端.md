# Ollama 切换 MLX 后端：Mac 推理 2 倍提速
- 来源: https://ollama.com/blog/mlx
- 日期: 2026-06-19
- 标签: #MLX #Ollama #推理引擎 #AppleSilicon

## 摘要
Ollama 0.19（2026-03-30 发布）在 Apple Silicon 上预览版默认切换到基于 Apple MLX 的推理后端，int4 量化下 prefill 达 1851 token/s、decode 达 134 token/s，相比此前 llama.cpp 后端约 2 倍提速。

## 要点
- Ollama 0.19 是 Mac 端最大架构变动，MLX 成默认推理后端
- 性能：int4 量化 prefill 1851 tok/s / decode 134 tok/s
- 新秀 Rapid-MLX（2026-03-23 发布）定位 Ollama 的 drop-in 替代，速度快 2–4.2x
- Apple 官方 mlx-lm 仍是最成熟栈：推理 + LoRA/QLoRA 微调 + OpenAI 兼容 server
- 影响：本地 LLM 工具链正向 MLX 收敛，llama.cpp 的 Mac 优势被蚕食
