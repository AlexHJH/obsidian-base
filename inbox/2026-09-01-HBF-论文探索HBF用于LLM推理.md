# 论文：探索 HBF 用于现代 LLM 推理的机会与挑战
- 来源: https://arxiv.org/abs/2608.13868
- 日期: 2026-09-01
- 标签: #HBF #LLM推理 #内存 #论文

## 摘要
arXiv 新论文系统评估 HBF 用于 LLM 推理的可行性：以低延迟 NAND 替代 HBM 的 DRAM，单堆叠容量提升超 14×（512GB vs HBM4 36GB），借助 die/plane 级并行实现接近 HBM4 的读带宽（约 1.6 TB/s/堆叠）。

## 要点
- 动因：Llama 4 Maverick 模型 746GB 远超单 GPU 288GB，多卡部署不可避免，容量瓶颈突出
- 混合配置（1×HBM+7×HBF）容量 3620GB、读带宽 11.2 TB/s；全 HBF（8 堆叠）达 4096GB、读带宽 12.8 TB/s（追平 HBM4）
- 软肋：写带宽仅约 0.11 TB/s（NAND 本质），需写合并/追加写策略规避耐久与写瓶颈

相关: [[HBF-NAND-AI推理内存2026]]
