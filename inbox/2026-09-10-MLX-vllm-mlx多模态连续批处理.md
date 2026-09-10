# vllm-mlx：Apple Silicon 原生多模态推理服务（连续批处理 + 内容前缀缓存）
- 来源: https://arxiv.org/abs/2601.19139
- 日期: 2026-09-10
- 标签: #MLX #Apple-Silicon #vLLM #多模态 #推理服务

## 摘要
vllm-mlx 原生构建在 MLX 之上，把连续批处理和 OpenAI 兼容 API 带到 Apple Silicon，并补上多模态缺口。文本模型吞吐比 llama.cpp 高 21–87%，多模态侧用内容哈希做前缀缓存来消除重复的视觉编码。

## 要点
- 连续批处理：16 并发下聚合吞吐 4.3x（llama.cpp 为顺序处理，无法扩展）
- 多模态前缀缓存：按内容哈希识别同一张图，重复图片查询 28x 加速、延迟 21.7s → <1s；64 帧视频分析 24.7x
- M4 Max 文本实测最高 525 tok/s；小模型优势最大（Qwen3-0.6B 1.87x），Nemotron-30B-A3B 仍 1.43x
- 差异化：比 mlx-lm 多连续批处理/OpenAI API/多模态缓存，比 llama.cpp 多连续批处理与多模态；单流速度本身与 mlx-lm 相当，价值集中在并发与多模态
- 关联: [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
