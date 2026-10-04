# Characterizing HBF for LLM Serving：把 HBF 放进 serving 栈做量化

- 来源: https://arxiv.org/abs/2609.39131 ; https://semiengineering.com/hbf-for-high-throughput-llm-serving-uc-berkeley-furiosaai
- 日期: 2026-09-30
- 标签: #HBF #LLM推理 #内存层级 #论文

## 摘要
UC Berkeley（Zack Yu、Coleman Hooper、Yakun Sophia Shao、Kurt Keutzer、Amir Gholami 等）联合 FuriosaAI（arXiv 2609.39131，9/30）从「LLM 服务」视角刻画 HBF：权重与 KV cache 共同挤压加速器内存，agentic 负载的重复交互进一步推高容量压力，HBF 可扩容量但代价是访问成本与有限写寿命。该文 10/2 被 SemiEngineering 技术论文栏收录。

## 要点
- 立论：serving 同时要装权重与活跃 KV，扩大 batch 能提升权重复用却要更多 KV 内存 → 瓶颈落在容量而非纯带宽
- 论文给出 HBF 的层次化（hierarchy）刻画，并在「可持续的 HBF 写寿命」约束下寻找服务效率优化空间
- 与 [[HBF-NAND-AI推理内存2026]] 中 FLINT / FlashAccel / DASH / HBFSim 属同一条「软硬件协同 + 评估底座」研究线
- 口径提示：本条仅依据 arXiv 摘要与引用信息整理，未读全文，具体实验数字待补读
- 关联 [[推理引擎格局-vLLM-SGLang]]
