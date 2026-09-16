# WriteScope / InplaceKVCache：写时就固定物理驻留，把 CPU-GPU 负载均衡变成调度
- 来源: https://arxiv.org/abs/2609.14507
- 日期: 2026-09-13
- 标签: #大模型推理 #KVCache #MoE #长上下文

## 摘要
单卡跑 MoE 长上下文必须把 KV 溢写到 CPU 内存，而溢出的 KV 有两种用途——传回 GPU 做 attention、或直接在 CPU 原地计算——二者要求相反的物理状态。InplaceKVCache 让「每个字节写时就固定物理驻留位置」，于是 CPU-GPU 负载均衡不再需要搬数据，退化为纯调度问题。配套 WriteScope 在 32GB 显存下支持 1M token 聚合规模。

## 要点
- 抽象：两维四区布局（设备亲和 × 访问模式）；WriteScope 沿序列维切 CPU/GPU 份额，用 roofline 性能模型定最优 CPU 份额，并在线反馈跟踪 CPU 成本漂移
- 实测：长上下文（≥8K）较 4 个复现基线几何平均加速 1.5–2.5×（A100）、1.4–1.7×（V100）；vLLM / SGLang / KTransformers 即使 KV 预算翻倍也无法端到端跑起来
- 与 DeepSeek-V4-Flash 原生稀疏注意力的组合已验证，说明「KV 格式设计」可与「注意力稀疏化」叠加
- 作者：国防科大（Enda Yu、Dezun Dong、Xiangke Liao）
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
