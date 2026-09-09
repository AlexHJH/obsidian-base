# HBF 定位再收敛：OXMIQ「HBM for the rack, HBF for the box」
- 来源: https://www.tomshardware.com/pc-components/ssds/hot-chips-2026-high-bandwidth-flash-promises-massive-bandwidth-and-capacity-but-its-usability-is-extremely-limited-new-memory-format-strikes-a-balance-between-hbm-and-nand-flash
- 日期: 2026-09-09
- 标签: #HBF #AI推理内存 #MoE

## 摘要
OXMIQ Labs 在 Hot Chips 2026 用 72-GPU 机架建模给出 HBF 的最终定位：容量瓶颈场景下 HBF 让单卡承载整模型（1 GPU 顶 8 GPU HBM），但带宽瓶颈场景 HBM 的 token 成本更优，结论是「HBM for the rack, HBF for the box」。

## 要点
- 三档规格明确：Grade1 8-Hi 256GB/384GB/s(8GT/s UCIe)、Grade2 512GB/1.536TB/s(16GT/s)、Grade3 512GB/3.072TB/s(32GT/s UCIe 2.0)；读块 64B–4KB、写/页 4KB
- MoE 特别适配：Kimi-K3 1.56TB 权重中 1.45TB(93%) 是专家权重，写一次读少 → 专家放 HBF、热权重放 HBM
- 「廉价内存 ≠ 廉价 token」：并发与生成率上升后 HBF 低带宽成瓶颈，全 HBM 机架成本/token 更低
- 关联 [[HBF-NAND-AI推理内存2026]]
