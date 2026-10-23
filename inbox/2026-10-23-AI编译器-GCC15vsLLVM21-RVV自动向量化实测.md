# Euro-Par 2026 实测：BananaPi-F3 上 GCC 15 vs LLVM 21 的 RVV 自动向量化
- 来源: https://arxiv.org/abs/2605.10860
- 日期: 2026-05-22 (v2)
- 标签: #RISCV #RVV #自动向量化 #编译器

## 摘要
用一套手写 RVV 1.0 汇编微基准在 BananaPi BPI-F3 与 Milk-V Jupiter（均为 SpacemiT X60，8 核、每核 2×256-bit RVV1.0）上标定性能上界并校准 perf 计数器，再据此在 6 个 HPC/ML proxy 应用上对比 GCC 15 与 LLVM 21 的自动向量化：GCC 15 在 4/6 上胜出，LLVM 21 只在 SGEMM/DGEMM 领先。

## 要点
- LLVM 的胜因是更激进的指令削减，用校准过的 `perf` 计数器在硬件上确认，不靠静态统计
- 两个成本模型还没吃透的瓶颈：谓词化（predication）开销、跨步 load
- 默认 LMUL 选择已接近最优（推翻"默认 LMUL 是主要损失来源"的常见直觉）；但 GCC 15 靠调大 LMUL 能拿到的额外收益明显多于 Clang 21
- LLVM 21 的 RISC-V 后端新增了一批 CPU 调度模型，其中包含 SpacemiT 调度模型 → 上游已开始认 K1 这种核
- 落到产品级：Google Qsim 状态向量模拟器上分别用手写 RVV intrinsics 与自动向量化对比，暴露当前 RVV 编译器对复杂访存模式仍不成熟
- 复现材料：github.com/KTH-ScaLab/rvv-evaluation（纯汇编微基准套件）
- 口径：读的是 v2 全文 HTML 摘要+方法+结论段，未逐节核对实验配置
- 与 [[AI编译器2026格局]]、[[AI芯片推理硬件2026全景]] 衔接
