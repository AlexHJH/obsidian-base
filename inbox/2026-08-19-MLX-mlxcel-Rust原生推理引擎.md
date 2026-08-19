# mlxcel：Rust 原生 MLX 推理引擎开源（Lablup）
- 来源: https://github.com/lablup/mlxcel
- 日期: 2026-05-28
- 标签: #MLX #Apple-Silicon #Rust #推理引擎

## 摘要
Lablup（Backend.AI 团队）开源 mlxcel：完全用 Rust 编写、直接调用 Apple MLX C++ 绑定的 LLM/VLM 推理引擎，无需 Python 运行时，原生支持 Apple Silicon（M1–M5）与 CUDA（DGX Spark）双目标。

## 要点
- 直接加载 mlx-community 格式 checkpoint 零转换，内置 OpenAI 兼容 server，v0.1.0 于 5/28 发布（Apache 2.0）
- 在 MLX 之上自研 TurboQuant 自定义 Metal kernel；M5 Max 128GB 基准：短 prompt prefill 更快、decode 吞吐接近 mlx-lm/mlx-vlm
- 与 Ollama/mlx-lm 选型对比见 kubesimplify 深度评测（M1 Max 实测），标志「Rust 免 Python」成为本地 LLM 新路线

相关: [[MLX生态2026]]
