# CUDA Agent：字节 Seed × 清华 AIR 的 Agentic RL 内核生成
- 来源: https://www.marktechpost.com/2026/08/17/bytedance-seed-and-tsinghua-air-introduces-cuda-agent-a-large-scale-agentic-rl-system-for-cuda-kernel-generation
- 日期: 2026-09-03
- 标签: #AI编译器 #AI-Agent #Kernel优化 #强化学习

## 摘要
ByteDance Seed 与清华 AIR 发布 CUDA Agent：把 LLM 放进真实 CUDA 开发环境（profiling + 正确性检查 + 权限沙箱），用 PPO 训练，让模型写出比编译器更快的 GPU kernel，直击"前沿模型能写出正确但偏慢的 CUDA"这一短板。

## 要点
- 基座 Seed1.6 在 KernelBench 通过率 74.0%，但仅 27.2% 快于 torch.compile（0.69× 几何平均）
- 训练后达 98.8% 通过率、96.8% 快于 torch.compile，2.11× 几何平均加速，领先 Claude Opus 4.5 / Gemini 3 Pro 约 40 分
- 关联 [[AI编译器2026格局]]、[[AI-Agent-2026格局]]
