# Proteus：张量切片稀疏传播分析（SPA）移植到 MLIR
- 来源: https://discourse.llvm.org/t/porting-the-sparsity-propagation-analysis-of-tensor-slices-to-mlir/92445
- 日期: 2026-10-07
- 标签: #AI编译器 #MLIR #稀疏 #静态分析

## 摘要
UFMG Compilers Lab 与 Imperial College London 把 Henrique 等人的 Sparsity Propagation Analysis（SPA）重写为 MLIR 实现 Proteus，面向 `linalg` / `tensor` 方言。SPA 是面向张量编译器 IR 的静态分析，跨计算图推断张量里的零区与 don't-care 区。

## 要点
- 三向传播（以 C = matmul(A, B) 为例）：forward 用 A、B 细化 C，lateral 用一侧输入细化另一侧，backward 用 C 反推 A、B
- 落地价值：分析结果能把计算密集 kernel 改写成带控制流的优化版本 → 稀疏性从「数据格式选择」升级为 IR 级静态事实
- 时间线：将在 2026 LLVM Developers' Meeting（Santa Clara）poster 环节展示，代码开源在 lac-dcc/proteus
- 关联 [[AI编译器2026格局]]
