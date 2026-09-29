# Sandisk 首颗 HBF die 已流片：2027 出样品、2028 量产
- 来源: https://www.trendforce.com/news/2026/08/14/news-sandisk-reportedly-tapes-out-first-hbf-product-targets-2027-samples-and-2028-production , https://xenospectrum.com/en/sandisk-hbf-die-tapeout , https://cryptobriefing.com/sandisk-hbf-architecture-ai-memory
- 日期: 2026-08-13（Sandisk Investor Day，TrendForce 8/14 转述）
- 标签: #HBF #NAND #AI推理内存 #OCP #UCIe

## 摘要
Sandisk 在 8/13 Investor Day 披露首颗 HBF die 已 tape out，AI 推理产品样品目标 2027 年、量产 2028 年；8 月初 OCP 刚发布首个 HBF 开放规范——标准制定与制造设计并行推进，节奏紧凑但相对原「2026H2 出内存样品」略有后移。

## 要点
- 规范要点（8/3–4 经 OCP 发布，距 2 月联盟成立仅约 6 个月）：8 层与 16 层 NAND 堆叠两种容量配置、单堆栈最高 512GB；带宽分 Grade 1–3，覆盖约 0.4–3.0TB/s；与处理器用 UCIe 互联，兼容类 HBM4 的封装尺寸与功耗曲线
- 定位与容量：NAND 让同面积容量达 HBM 的 8–16 倍，成本功耗同区间；Llama 3.1 405B 上读带宽距「无限容量 HBM」2.2%
- 经济性主张（厂方分析口径）：4 张带 HBF 的 GPU 可跑出 8 张 HBM GPU 的 token 产出 → 减少 GPU 数量、压低推理基础设施成本
- 待核点：内存样品是否已交付未见明确确认；软件栈仍是空白——内存分配、预取、闪存耐久监控需框架层（例如 vLLM 侧）专门支持
- 关联 [[HBF-NAND-AI推理内存2026]]、[[推理引擎格局-vLLM-SGLang]]
