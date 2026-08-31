# vllm-mlx：Apple Silicon 原生多模态 LLM 推理
- 来源: https://arxiv.org/html/2601.19139v2
- 日期: 2026-08-31
- 标签: #MLX #vLLM #多模态 #Apple-Silicon

## 摘要
vllm-mlx 框架在 MLX 上原生实现 LLM 与多模态 LLM 推理，文本模型吞吐较现有方案高 21%–87%，并用内容哈希缓存视觉 embedding + KV cache，消除重复视觉编码。

## 要点
- 利用统一内存做零拷贝 cache 管理，配合懒求值融合算子、原生量化
- 内容哈希识别相同图像，缓存视觉 embedding 与 KV cache，多请求间去重
- 填补 PyTorch MPS 优化不足、多模态工作负载服务缺位
- 相关: [[MLX生态2026]] [[推理引擎格局-vLLM-SGLang]]
