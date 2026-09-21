# Hot Chips 2026：HBF 不是「便宜的 HBM」——OXMIQ 给出可用性边界
- 来源: https://www.tomshardware.com/pc-components/ssds/hot-chips-2026-high-bandwidth-flash-promises-massive-bandwidth-and-capacity-but-its-usability-is-extremely-limited-new-memory-format-strikes-a-balance-between-hbm-and-nand-flash
- 日期: 2026-08-26
- 标签: #HBF #HBM #AI推理 #内存分层 #HotChips

## 摘要
GPU IP 公司 OXMIQ 在 Hot Chips 2026 上系统性质疑 HBF 的普适性：HBF 应被当作「高容量内存」而非「廉价 HBM」，它只在容量受限时占优，带宽/吞吐成为瓶颈后会输给 HBM。结论一句话——「HBM for the rack, HBF for the box」。

## 要点
- 规格分三档：Grade 1（8-Hi 256GB，8 GT/s UCIe，384 GB/s）、Grade 2（512GB，16 GT/s，1.536 TB/s）、Grade 3（512GB，32 GT/s UCIe 2.0，3.072 TB/s）；仅 Grade 3 可对标 HBM4E，且上市时间未知
- 等成本/功耗下建模 72-GPU 机架跑 Kimi-K2（1T，FP4）：全 HBM = 20.7 TB / 1584 TB/s；全 HBF = 294.9 TB（14x 容量）/ 922 TB/s；混合 = 89.3 TB / 279–1418 TB/s
- 全 HBF 可单卡装一个模型实例（72 实例/机架）而全 HBM 需 8 卡装一个（9 实例/机架）——省的是「装模型的卡」，不是 token 成本；用户数/生成速率上来后 HBM 机架单 token 成本更低
- 适合 HBF 的场景：MoE 专家权重（Kimi-K3 中 93% 权重为专家，写一次低频读，放 HBF 可减少 expert-parallel all-to-all）、sparse attention 的长上下文 KV cache（按步取用）
- 主要限制：批量增大后专家热度扁平化，冷热分层失效、HBM cache 被 working set 击穿；且 HBF 需 64KB 读 / 1MB 写才能打满带宽，vLLM 需专门支持（分配、预取、寿命监控），是硬件厂+加速器厂+推理框架的三方工程
- 关联 [[HBF-NAND-AI推理内存2026]] [[推理引擎格局-vLLM-SGLang]] [[AI芯片推理硬件2026全景]]
