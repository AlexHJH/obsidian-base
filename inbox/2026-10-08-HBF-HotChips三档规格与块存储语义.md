# Hot Chips 2026 给 HBF 划边界：三档规格与「块存储语义」
- 来源: https://www.tomshardware.com/pc-components/ssds/hot-chips-2026-high-bandwidth-flash-promises-massive-bandwidth-and-capacity-but-its-usability-is-extremely-limited-new-memory-format-strikes-a-balance-between-hbm-and-nand-flash ; https://chipsandcheese.com/p/hot-chips-2026-applying-high-bandwidth
- 日期: 2026-08（Hot Chips 2026，OXMIQ Labs 教程）
- 标签: #HBF #HotChips2026 #内存层级 #UCIe

## 摘要
OXMIQ Labs 在 Hot Chips 2026 的门槛判断是：HBF 无法在绝大多数负载替代 HBM——只在「容量受限」时划算，带宽受限时反而有害。教程同时把规范拆成三档，容量 256–512GB、带宽 384GB/s–3.072TB/s，并强调 HBF 是块存储语义，现有按 DRAM 假设写的框架需要大改。

## 要点
- 三档规格：Grade 1 = 8-Hi 256GB + UCIe 8GT/s + 384GB/s；Grade 2 = 512GB + 16GT/s + 1.536TB/s；Grade 3 = 512GB + 32GT/s（UCIe 2.0）+ 3.072TB/s
- 语义代价：改一个字节要读-改-写整块（更接近块设备而非可字节寻址内存），软件栈改造量是落地主成本
- 机会点一：用 HBF 的容量做权重复制，减少多卡间 scatter/gather——DMA 搬运比跨设备通信便宜
- 机会点二：把「冷但大」的数据集放进带内高容量层，避免 HBM 缓存被频繁专家换入拖累
- 关联 [[HBF-NAND-AI推理内存2026]]
