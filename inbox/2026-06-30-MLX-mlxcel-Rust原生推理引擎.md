# mlxcel：Rust 原生 MLX 推理引擎，Apple Silicon 新选手
- 来源: https://github.com/lablup/mlxcel
- 日期: 2026-06-30
- 标签: #MLX #Apple-Silicon #推理引擎 #Rust #mlxcel

## 摘要
Lablup 5 月开源 mlxcel v0.1.0，Rust 原生调用 MLX C++ 运行时，非 Python 项目。内置 OpenAI 兼容 HTTP server、Homebrew 一键安装，M1–M5 全系支持。文本 decode 达 mlx-lm 的 99%，VLM decode 中位数 1.79x mlx-vlm，95% 模型优于 mlx-lm。

## 要点
- `mlxcel serve -m <model>` 即起兼容 API，无需转换 mlx-community 格式
- 直接 via Rust FFI → MLX C++ runtime，无 Python 开销
- 已集成 Backend.AI / AI:GO 平台，定位本地推理基础设施
- 关联 [[MLX生态2026]]
