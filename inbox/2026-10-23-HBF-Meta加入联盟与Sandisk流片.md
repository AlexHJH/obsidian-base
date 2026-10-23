# HBF 联盟扩容到 Meta：Sandisk 首个 die 已流片，样品 2026H2、推理设备 2027 初
- 来源: https://photoncap.net/p/beside-above-or-beyond-the-ai-memory
- 日期: 2026-08-16（Sandisk 投资者日 2026-08-13）
- 标签: #HBF #NAND #AI内存 #Sandisk #OCP

## 摘要
Sandisk 8/13 投资者日的「HBF Consortium Milestones」幻灯片显示 Meta 已加入 HBF 联盟，成为继 Google、Tenstorrent 之后的第三家成员——两家自研 AI 芯片的云厂都在 NAND 基内存标准上署名。同页给出节奏：首个 HBF die 完成 tape-out，客户样品 2026H2、推理设备 2027 年初。Micron 官方不参与这条路线。

## 要点
- 规范口径（OCP 2026-08-03 首发）：8 层 / 16 层 NAND 堆叠，最高 512GB，三档带宽 0.4–3.0 TB/s，用 UCIe 连 CPU/GPU
- 路线定位：把 HBM 的堆叠公式搬到 NAND，做「比 SSD 宽得多、比 HBM 大得多」的中间层，不是 HBM 替代品
- 三家「堆叠」含义不同：Sandisk/SK hynix 是 NAND 叠成内存层；Samsung zHBM 是把内存叠到加速器正上方；Micron 在 HBF 上明确缺席（招聘帖 HBF 关键词 0 次）
- 物理依据：NAND 微秒级延迟 vs DRAM 纳秒级 → 容量层允许「离得远」，因此可放到光互联覆盖的距离上
- 推论：若堆叠路线收敛，增量价值可能更多落在光互连层而非内存厂
- 与本库既有条目衔接：规范细节见 [[HBF-NAND-AI推理内存2026]]，工程侧 MARS 内存解耦见同主题 10-21 条目
- 口径：付费文可读部分 + 投资者日幻灯片转述，未读全文与原始 spec 文档
