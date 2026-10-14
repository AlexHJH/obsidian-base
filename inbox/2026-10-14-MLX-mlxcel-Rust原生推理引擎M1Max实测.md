# mlxcel：Rust 原生 MLX 推理引擎，M1 Max 实测与 mlx-lm 打平
- 来源: https://blog.kubesimplify.com/mlxcel-rust-native-inference-engine-tested-on-m1-max ; https://zachrattner.com/projects/ai-mac-cluster/mlx-vs-ollama
- 日期: 2026-06-0x（评测）；v0.1.0 首发 2026-05-28，v0.1.3 于 05-30
- 标签: #MLX #AppleSilicon #推理引擎 #Rust

## 摘要
mlxcel 是 Lablup（Backend.AI 团队）出的 Rust 二进制推理引擎，直接调用 Apple MLX C++ runtime，不经 Python，自带 OpenAI 兼容 HTTP server，Homebrew 一条公式安装。第三方在 M1 Max 64GB 上与 mlx-lm、Ollama 0.20.7 对跑，结论是「与 mlx-lm 同档、比 Ollama 快 ~1.3×」，但优势主要来自 MLX 内核本身而非 Rust。

## 要点
- Llama-3.2-3B-Instruct-4bit decode：mlxcel 63.33 vs mlx-lm 67.63 vs Ollama 48.73 tok/s（3 次均值，2026-05-31）
- Qwen2.5-7B-4bit 长 prompt（120 词）decode：mlxcel ~55、mlx-lm ~52、Ollama ~42 tok/s；两者差距在长 prompt 下仍≈1.3×
- 7B prefill 复测中 mlxcel 与 mlx-lm 回到同一档（~97 vs ~102 tok/s），原始一轮的「mlxcel 领先」未能复现，且冷启动方差大（56/110/124）
- 生态信号：Ollama 0.19 起把 Apple Silicon 后端从 llama.cpp 换成 MLX，同机 decode 3.19→23.39 tok/s；MLX vs llama.cpp 的差距本质是内核/FlashAttention 实现差异
- 口径：单机单作者实测、样本小；mlxcel 早期版本（0.1.x）功能仍在补齐，适合 CI/嵌入式集成而非取代 mlx-lm
- 关联 [[MLX生态2026]]、[[AI芯片推理硬件2026全景]]、[[推理引擎格局-vLLM-SGLang]]
