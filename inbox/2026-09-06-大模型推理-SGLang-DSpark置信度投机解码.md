# SGLang DSpark：置信度驱动的投机解码
- 来源: https://github.com/sgl-project/sglang/releases
- 日期: 2026-09-06
- 标签: #大模型推理 #SGLang #投机解码 #DeepSeek

## 摘要
SGLang 发布 DSpark 投机解码算法：分块半自回归起草，按 draft 自身置信度动态决定验证窗口大小，取代固定 draft 长度。

## 要点
- DeepSeek-V4-Pro 于 TP8 B300 (bs=1) 达 383.7 tok/s、accept length ~5
- 用 `--speculative-algorithm DSPARK` 启用；同期推 SGLang-Diffusion 实时视频生成
- 关联 [[推理引擎格局-vLLM-SGLang]]
