# DeepSeek 开源 V4-Flash-Vision-Exp：V4 家族首个原生视觉模型（305B MIT）
- 来源: https://cellcog.ai/blog/deepseek-v4-flash-vision-exp
- 日期: 2026-09-10
- 标签: #大模型推理 #DeepSeek #多模态 #开源权重

## 摘要
DeepSeek 8/31 在 Hugging Face 以 MIT 协议开源 V4-Flash-Vision-Exp（305B），是 V4-Flash 家族首个原生视觉模型，在文本骨干上叠加视觉编码器+对齐层，多模态 Agent 任务逼近 Opus 4.8。

## 要点
- 原生视觉（非外挂 vision tower），保留 DFlash attention / MoE / Hyper-Connections / DSpark 前向路径
- README 内置 vLLM/SGLang 启动命令 + 最小 PyTorch 脚本；vLLM 9/2 合并支持（PR 54566）
- 一周 Hugging Face 下载破 20.9 万；量化适配 llama.cpp / Ollama / LM Studio
- 关联 [[推理引擎格局-vLLM-SGLang]]
