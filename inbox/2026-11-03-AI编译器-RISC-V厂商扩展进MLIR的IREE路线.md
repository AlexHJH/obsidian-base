# MLIR 里的 RISC-V 矩阵/向量厂商扩展：IREE 在 LLVM DevMtg 2026 给出的工程答案
- 来源: https://www.roofline.ai/news/news-roofline-llvm-developers-meeting-2026
- 日期: 2026-10-28
- 标签: #RISC-V #MLIR #IREE #AI编译器 #厂商扩展

## 摘要
LLVM 美国开发者大会（10/26–28，Santa Clara）上 Roofline 的 Ege Beysel 讲《Bringing RISC-V Matrix and Vector Vendor Extensions into MLIR-based ML Compilers》（10-28 14:15 PDT），以 IREE 为开源案例讲厂商自定义矩阵/向量指令如何接进 ML 编译器。该公司同场还讲 data-tiled 卷积（10-27）与 Hexagon NPU 重定向（10-28），三场合起来是一套「把 MLIR 编译器搬到新硬件」的方法论。

## 要点
- 问题定义：RISC-V 允许厂商加指令，但要让编译器用上必须同时动 MLIR 与 LLVM 两侧，不是加个 intrinsic 就完事
- 讲者称对比三条路径（原文枚举段落抽取被拦，只确认是 "three approaches"，具体分法待核后再补）
- 技术底稿可对照其 arXiv 2605.12445（VLA 可伸缩向量长度打包布局）：接缝在 layout 与 tiling/fusion/vectorization 三处变换，不在前端
- 另一条实证线 arXiv 2603.17800 用自定义 xDSL lowering 生成 RVV gemm，在 K230 / BananaPi F3 上对 OpenBLAS 与 BERT-Large 负载实测
- 同场信号：Triton 侧也在做运行时扩展系统（"Extensions All the Way Down"），与 3.7 起的插件机制同一条线
- 对本项目：Zvt/VME 若要进上游 MLIR，现实路径是「社区方言 + layout/vectorize 改造」，而非自造前端

关联：[[AI编译器2026格局]]、[[RISC-V矩阵扩展两年进展-2024-2026]]
