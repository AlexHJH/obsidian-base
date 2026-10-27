# Rapid-MLX 0.16.8：Flint 40B 上 Mac、并发短对话不再排队
- 来源: https://github.com/raullenchai/Rapid-MLX/releases ; https://rapidmlx.com/changelog/0.16.8
- 日期: 2026-10-22
- 标签: #MLX #AppleSilicon #投机解码 #推理引擎

## 摘要
0.16.8（10/22）加入 Flint 官方 INT4 checkpoint 支持，并让多个并发短 MTP 对话同时 decode；0.16.9（10/25）转向桌面端体验与模型加载可靠性（按校验后的 revision 下载、失败可恢复）。

## 要点
- Flint 实测（M5 Max 36GB / mlx 0.32.4 / greedy / 256 输出 token）：1K 上下文 131 tok/s decode、TTFT 0.35–0.38s、峰值约 24GB；8K 上下文降到 118.5 tok/s、TTFT 2.2s
- 4 路并发 ~1K 请求聚合 193 tok/s；重复前缀命中把 TTFT 从 0.46s 压到 0.15s
- 并发短对话（M3 Ultra / qwen3.8-27b-4bit）聚合吞吐 37.6 → 73.8 tok/s，代价是单请求 TTFT 由 0.6/4.0/7.3/10.8s 拉平到各约 1.4s —— 用延迟换吞吐，单流性能不变
- 其余：DeepSeek V4.1 投机验证开销下降；运行中的 server 无需重启即可发现更新；并发仍受内存准入上限约束
- 口径：项目自测、特定工作负载与硬件，未做第三方复核

## 关联
[[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
