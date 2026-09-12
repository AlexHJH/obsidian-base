# mlxcel：Rust 原生 MLX 推理引擎，M1 Max 实测与 mlx-lm 持平
- 来源: https://blog.kubesimplify.com/mlxcel-rust-native-inference-engine-tested-on-m1-max
- 日期: 2026-09-12
- 标签: #MLX #Apple-Silicon #Rust #推理引擎

## 摘要
第三方项目 mlxcel 在 MLX 之上用 Rust 重写推理引擎，作者在 M1 Max 干净环境复测：解码速度与上游 mlx-lm 基本持平（约 55 vs 52 tok/s），比 Ollama 快约 1.3 倍，短 prompt 交互手感更好。

## 要点
- 量化 3B 模型解码约 63 tok/s，与 mlx-lm 同档；长 prompt 上对 Ollama 的 1.3x 差距保持稳定
- 自动推导 wired memory 上限（M1 Max 上 51.8GB），可用 `MLXCEL_WIRED_LIMIT` 覆盖或禁用
- 定位：Apple Silicon 上 mlx-lm / Ollama 之外的第三种运行时，卖点是 Rust 的部署形态而非更快
- 关联: [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
