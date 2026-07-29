# Triton 3.7.1 发布：MLIR 后端重写完成
- 来源: https://github.com/triton-lang/triton/releases
- 日期: 2026-07-29
- 标签: #AI编译器 #Triton #MLIR #GPU

## 摘要
Triton 3.7.1 于 6 月 18 日发布，最大变化是后端全面重写为 MLIR，支持 back-to-back matmul kernel（如 Flash Attention）。与此同时 TileLang 在 MLSys 2026 社区关注度飙升，Wave（Python DSL 编译器）入选工业 Track，编译器竞争进入「tile 级 DSL + 自动调度」新阶段。

## 要点
- Triton 3.7.1 后端 MLIR 化完成，性能提升 + bug 修复，支持 Flash Attention 类 kernel
- TileLang：tile 级 DSL，H100 比 Triton 平均 3.02×，已集成 Z3 定理证明器做自动正确性验证
- Wave（Modular）：符号化 Python DSL，入选 MLSys 2026 工业 Track 编译器专场
- 趋势：编译器从「手写 schedule」走向「自动搜索最优 tiling + 形式化验证」
