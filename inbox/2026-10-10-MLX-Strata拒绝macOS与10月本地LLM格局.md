# Strata 明确拒绝 Apple Silicon：10 月本地 LLM 热度回到 CUDA

- 来源: https://pub.towardsai.net/best-local-llms-for-your-mac-in-october-2026-24gb-to-128gb-picks-7d6f32819dd5
- 日期: 2026-10（Towards AI，上周回顾）
- 标签: #MLX #AppleSilicon #本地推理 #生态

## 摘要
10 月上旬本地 LLM 圈最热的话题不在 Mac 上：一个用单张 RTX 4090 跑 Qwen 3.8 Flash-Next 的帖子在 HN 冲到 800 分，引擎 Strata 只支持 NVIDIA/AMD（Windows/Linux）。维护者两次关闭 Apple Silicon 请求，第二次回复是「macOS / Apple Silicon isn't planned. Closing.」——与 MLX 侧「一切皆本地」的叙事形成对照。

## 要点
- 信号一：新引擎的默认目标平台仍是 CUDA/ROCm，Apple Silicon 要单独立项而非顺带适配
- 信号二：Mac 侧优势靠 Apple 官方栈（MLX / MLX-LM Server）与「把大模型塞进统一内存」，而不是跟上最快的新引擎
- 口径：HN 800 分是热度而非基准；4090 仅 24GB 显存，跑得动的仍是量化中小模型
- 判据延续 [[MLX生态2026#Apple Silicon LLM 推理运行时选型框架（2026-07 归档）]]：选 Mac 按「能装下多大模型」选，不按吞吐选
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
