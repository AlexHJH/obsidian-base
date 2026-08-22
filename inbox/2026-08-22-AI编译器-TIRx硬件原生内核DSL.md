# TIRx：Apache TVM 推出的硬件原生内核 DSL/编译器
- 来源: https://tvm.apache.org/2026/06/22/tirx
- 日期: 2026-08-22
- 标签: #AI编译器 #TVM #Triton #GPU内核

## 摘要
Apache TVM 社区发布 TIRx（"tier-ex"），一个构建在 TVM 之上的开源硬件原生内核 DSL 与编译器，面向快速演进的前沿 ML 内核。它把抽象边界放得比 Triton 更低、更显式：编排（流水线、同步、内存布局）留在源码中，可复用的 tile 原语交给编译器分派。

## 要点
- 定位：低于 TileLang/Triton 的抽象层；新硬件先以 intrinsic 进入，稳定后再提升为 tile primitive
- 三大要素：execution scope（线程/warpgroup 粒度）、storage-first 布局（Shard/Replica/Offset）、tile primitive dispatch（TMA/ldmatrix/WGMMA/tcgen05 等自动选型）
- 已随 TVM 0.25 发布 PyPI wheel、GEMM/attention 内核库（tirx-kernels）与 CMU MLSys 课程；与 TileLang 社区合作作为其新底层
- 面向三类用户：专家手写内核、agent 生成内核、megakernel 系统

相关: [[AI编译器2026格局]]
