# Ollama 0.19 全面拥抱 MLX，Apple Silicon 推理速度飙升
- 来源: https://ollama.com/blog/mlx
- 日期: 2026-06-14
- 标签: #MLX #Apple-Silicon #Ollama #LLM推理

## 摘要
Ollama 0.19 版本将 Apple Silicon 上的推理后端从 llama.cpp 切换为 Apple 原生的 MLX 框架，得益于统一内存架构(UMA)和 MLX 的图编译器/JIT 编译，MoE 模型推理速度提升 2-7 倍；同时引入 NVIDIA NVFP4 量化格式，兼顾精度与内存带宽。

## 要点
- M1 Max (64GB) 上 decode 从 3.19 tok/s 提升至 23.39 tok/s，约 7x 加速
- 自动路由：GGUF→llama.cpp，safetensors→MLX，无需手动切换
- NVFP4 4-bit 量化格式带来云端级别的本地推理体验
- 当前短板：Flash Attention 长上下文(>30K)不及 llama.cpp，多 LoRA 服务仍在 alpha
- Rapid-MLX 作为 Ollama 的即插即用替代品，在 M3 Ultra 上快 2-4.2x
