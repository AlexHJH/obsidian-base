# TileLang：基于 TVM 的 tile 级 kernel DSL（ICLR 2026）
- 来源: https://openreview.net/pdf/e851ad3eaca44bc18d67d753b03b3664ca2b9e9c.pdf
- 日期: 2026-06-26
- 标签: #AI编译器 #TileLang #TVM #Triton #GPU-kernel #ICLR2026

## 摘要
北大/帝国理工/TileAI/微软研究院 ICLR 2026 论文。TileLang 是 tile 级 kernel 编程 DSL，Pythonic 语法、编译栈建在 TVM 之上，把调度空间（线程绑定/布局/tensorize/流水线）与数据流解耦——开发者只写数据流，优化交给编译器。H100 上比 Triton 平均快 3.02×（最高 10.58×），AMD GPU 平均 2.65×。

## 要点
- 显式原语控制显存放置/数据搬运/并行调度；融合 tile 级数据流图（FTG）统一「tile 推荐」（硬件感知默认）与「tile 推断」（约束传播自动补全）
- 已验证 H100(Auto TMA/WGMMA)/A100/V100/RTX 4090、AMD MI250/MI300X；示例含 FlashMLA on Hopper、FlashAttention、Dequant GEMM
- 与 Triton 互补：Triton 细粒度控制有限，TileLang 补足显式低层控制；TVM/Triton/TileLang 已在 HyperAI 编译器沙龙同台，跨硬件统一编译生态成共识
- 关联 [[AI编译器2026格局]]
