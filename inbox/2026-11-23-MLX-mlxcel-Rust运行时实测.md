# mlxcel：Rust 直调 MLX 的运行时，M1 Max 上解码与 mlx-lm 打平而非超越
- 来源: https://blog.kubesimplify.com/mlxcel-rust-native-inference-engine-tested-on-m1-max
- 日期: 2026-05-29
- 标签: #MLX #AppleSilicon #推理部署 #实测 #Rust

## 摘要
mlxcel v0.1.0（2026-05-28）是单二进制 Rust 运行时，直接调 MLX C++ 层，自带 OpenAI 兼容 server（连续批处理 + 前缀缓存）。作者在 M1 Max 64GB 上三方对照 mlx-lm 与 Ollama，结论是解码速度与 mlx-lm 平手（差 ~6%），对 Ollama 快 1.3×。

## 要点
- Llama-3.2-3B-4bit：mlxcel 63.33 / mlx-lm 67.63 / Ollama 48.73 tok/s（3 次均值，2026-05-31 清洁扫描）；Qwen2.5-7B-4bit：31.33 / 31.80 / 24.23 → 优势来自 MLX 内核而非这个 wrapper
- README 里的 "2.70× prefill" 是 M5 Max 专属；M1 Max 长 prompt 重扫时三者在 ~420–440 tok/s 基本持平，且 mlxcel 波动大（298–592）——作者自己把这条点出来，值得记
- TurboQuant 4-bit KV cache 在旧芯片上是负优化：63.33 → 17.48 tok/s（慢 3.6×），官方文档只说 "旧路径瓶颈可能不同"
- 工程价值在部署形态：Homebrew 单公式、无 Python 环境、server 秒级启动；wired memory 上限自动取 51.8 GB，可用 `MLXCEL_WIRED_LIMIT` 覆盖
- 判读：Apple Silicon 本地推理的瓶颈已从"框架选择"转到"内核 + 内存带宽"，换语言（Python→Rust）基本不带来性能，只带来部署简洁
- 关联 [[MLX生态2026]]
