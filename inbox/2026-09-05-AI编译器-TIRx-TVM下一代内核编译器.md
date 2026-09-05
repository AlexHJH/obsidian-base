# Apache TVM 发布 TIRx：硬件原生 ML 内核编译器栈
- 来源: https://tvm.apache.org/2026/06/22/tirx
- 日期: 2026-09-05
- 标签: #AI编译器 #TVM #TIRx #内核生成

## 摘要
Apache TVM 推出下一代内核级编译器 TIRx——一个硬件原生 DSL + 编译器栈，瞄准「快速演进的 kernel × 快速演进的硬件」交界地带，同时服务专家手写、Agent 生成与 megakernel 三类内核。

## 要点
- 内核把编排（流水线/同步/后端 intrinsic）保留在硬件原生源码中，把执行域/张量布局/tile 原语暴露给编译器；storage-first 张量布局（TileLayout/ComposeLayout）
- Python 嵌入式 DSL（`@T.jit`/`@T.prim_func`），已编译到 GPU 与专用 AI 加速器，基座仍是 TVM 编译器基础设施；示例覆盖 GEMM/attention/低精度算子（Blackwell）
- 相关: [[AI编译器2026格局]]
