# Llama 4 Scout on MLX：Apple Silicon 本地跑 10M 上下文 MoE
- 来源: https://www.sitepoint.com/llama-4-scout-on-mlx-the-complete-apple-silicon-guide-2026
- 日期: 2026-08-21
- 标签: #MLX #Apple-Silicon #Llama #MoE #本地推理

## 摘要
SitePoint 实测 Meta Llama 4 Scout（MoE，16 experts、109B 总参/17B 激活、10M token 上下文）在 MLX 上的本地推理：统一内存免 GPU-CPU 拷贝，M2 Pro 起可达交互速度，prompt eval/生成比 llama.cpp 约快 2–2.5×。

## 要点
- Llama 4 Scout：16 experts MoE，17B/109B 参数，10M token 上下文（消费级实际受限）
- 统一内存架构消除离散 GPU 拷贝瓶颈，M2 Pro+ 即交互式速度，llama.cpp 仅批量可用
- 比 llama.cpp 约 2–2.5× 提速；长上下文或量化过载触发 swap 时生成会崩
- memory_pressure 是判断是否溢出的关键监控指标
