# mlx-optiq / OptiQ：按层 KL 敏感度分配位宽的 MLX 混合精度量化工具链
- 来源: https://mlx-optiq.com , https://mlx-optiq.com/docs/faq , https://pypi.org/project/mlx-optiq
- 日期: 2026-09-26（项目 2026-03-25 首发 PyPI 0.0.1，仍在快速迭代）
- 标签: #MLX #Apple-Silicon #量化 #KV-Cache #推测解码

## 摘要
第三方 MLX 原生工具链 mlx-optiq（OptiQ）不做统一位宽量化：先在校准集上测每层的 KL 敏感度，再用贪心背包在目标平均 BPW 预算内逐层分配 4/8bit，产物多数可直接用 stock `mlx-lm` 跑。它把「混合精度」从权重延伸到 KV cache，并捆绑 MTP 投机头与敏感度感知 LoRA。

## 要点
- 效果示例（官方口径）：`gemma-4-e4b` 均匀 4bit 的 GSM8K 仅 23.5%，同等磁盘占用下 OptiQ 提到 55.5%；HF 上已有 Qwen / Gemma-4 / Nemotron / Mistral / MiniCPM5 等 12+ 家族量化件，覆盖 1B dense 到 35B-A3B
- KV cache 单独跑一遍逐层敏感度：第 0 层 KV 敏感度约为均值的 56 倍，统一 4bit KV 会毁长上下文 → 只把少数层提位宽，平均 4bit 即可在 hash-hop 上对齐 fp16
- 配套：OptiQ Lab 本地网页工作台（chat/量化向导/SFT·DPO/双模型 Arena）、`optiq serve` 同时说 OpenAI 与 Anthropic 协议、捆绑 MTP 头约 1.4x 投机解码；CLI 形如 `optiq convert <model> --target-bpw 5.0 --candidate-bits 4,8`
- 定位对比：与 oMLX 的 oQ(imatrix) 同属逐层非均匀量化，差别在信号来源（imatrix vs 逐层 KL）；社区反馈两者原始 4bit 质量接近，OptiQ 差异更多在周边（KV、MTP、serve）
- 已知边界：站点数字均为自测、未见第三方独立复核；视觉层不支持量化，当前量化件只做文本推理
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
