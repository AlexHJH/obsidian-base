# SK Hynix 提出 H³：单 interposer 上 HBM+HBF 混合加速 LLM 推理
- 来源: https://ieeexplore.ieee.org/document/11371745
- 日期: 2026-09-07
- 标签: #HBF #AI推理内存 #HBM #LLM推理

## 摘要
SK Hynix 在 IEEE 论文提出 H³ 混合架构：HBM 供速度、HBF 供容量，二者在同一 interposer 上直连 GPU，以应对长序列 LLM/agent 推理的内存容量瓶颈。

## 要点
- HBF（NAND 基）具 HBM 级带宽 + 更大容量，但代价是更高时延、更低写寿命、更高功耗
- H³ 用 HBM 承载热数据/中间结果，HBF 承载超大 KV cache 等冷数据，取长补短
- 与前两天结论呼应：HBF 价值在于「容量点」而非 HBM 全面替代，软硬协同是关键
- 相关: [[HBF-NAND-AI推理内存2026]] [[推理引擎格局-vLLM-SGLang]]
