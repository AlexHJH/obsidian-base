# Triton for MTIA：弥合定制 AI 加速器的编程模型鸿沟
- 来源: https://arxiv.org/abs/2608.00325
- 日期: 2026-09-07
- 标签: #AI编译器 #Triton #MLIR #定制加速器

## 摘要
Meta 发布面向自研 MTIA-2i 推理芯片的 Triton 编译器（基于 MLIR），把 Triton 的 tile 级编程模型下沉到定制 NPU 类加速器，复用 MLIR 生态降低新硬件适配成本。

## 要点
- 核心难点是"编程模型鸿沟"：Triton 的 tile 抽象与 MTIA 定制架构的并行/访存模型不直接匹配
- 高复杂度 kernel 不能只靠编译器自动优化，需手写 kernel 配合 MTIA 特化（显式数据复用、重映射 workload→PID）
- 验证 Triton 作为定制芯片通用编程层的可行性，呼应"编译器核心小、后端扩展"路线
- 关联: [[AI编译器2026格局]] [[AI芯片推理硬件2026全景]]
