# MLIR Workshop @ EuroLLVM 2026：Auto-Tuning 与编译工具链新进展
- 来源: https://llvm.swoogo.com/2026eurollvm/agenda
- 日期: 2026-06-17
- 标签: #MLIR #AI编译器 #AutoTuning #EuroLLVM #Intel-GPU

## 摘要
第7届 MLIR Workshop 于 EuroLLVM 2026 (4月, 都柏林) 举行，聚焦 MLIR 在生产环境中的落地。Intel 团队展示面向 Battlemage GPU 的端到端 auto-tuning 调度方案，无需手动 tiling 即可达到 SOTA 性能。

## 要点
- Intel 提出 Linalg→LLVM IR 的 auto-tuning 框架，通过 tuneable "knob" ops 编码优化空间，自动搜索最优 tiling 参数
- 无需用户指定 tiling，上游 MLIR 即可达到 SOTA 框架的 kernel 性能
- ASTER: 基于 MLIR 的汇编工具链，将 MLIR 基础设施用于底层汇编开发
- Roofline 团队开源 mlir-track-src，追踪 MLIR pass pipeline 中的操作变换
