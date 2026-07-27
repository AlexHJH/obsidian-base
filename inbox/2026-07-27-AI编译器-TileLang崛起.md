# TileLang：AI编译器新势力崛起
- 来源: https://github.com/tile-ai/tilelang
- 日期: 2026-07-27
- 标签: #AI编译器 #TileLang #TVM #算子编程

## 摘要
TileLang 是基于 TVM 的新型算子编程语言，将数据流定义与硬件调度解耦，让编译器自动处理线程绑定、内存布局、张量化等优化。相比 Triton 在主流 GPU 上最高可获 6 倍性能提升，已获 6.6k GitHub stars。

## 要点
- 核心设计：显式 tile 级原语 + 自动布局推导 + 自动流水线编排，开发者只需关注数据流
- 集成 Z3 定理证明器，支持 SMT 符号推理进行自动正确性验证
- v0.1.11（2026-06）已支持 CuTeDSL backend，可编译到 NVIDIA CUTLASS
- 7月 HyperAI 沙龙 TVM/Triton/TileLang 首次同台竞技，标志三足格局初现
