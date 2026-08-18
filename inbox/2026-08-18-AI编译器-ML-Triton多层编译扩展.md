# ML-Triton: 面向 Intel GPU 的 Triton 多层编译扩展
- 来源: https://arxiv.org/abs/2503.14985
- 日期: 2026-08-18
- 标签: #AI编译器 #Triton #MLIR

## 摘要
ML-Triton 将 Triton 单层 block 级编译扩展为 workgroup→warp→intrinsic 的多层 lowering，对齐 Intel Ponte Vecchio (PVC) GPU 的层级结构，并扩展 Triton 语言支持用户自定义编译提示与 warp 级编程。

## 要点
- 多层 lowering 对齐 GPU 层级（workgroup/warp/intrinsic），逐级分解算子
- 扩展 Triton 语言，支持 warp 级编程与编译提示，无需等编译器更新即可获开箱性能
- 面向 Intel PVC 的异构后端适配思路，体现"分层而非条件分支"的多后端方法
