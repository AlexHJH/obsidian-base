# Nvidia GPU 微架构演进：Volta → Blackwell

- 整理自: [[inbox/2026-06-13-TensorCore-演进史]], [[inbox/2026-06-13-Blackwell-微架构逆向]]
- 标签: #Nvidia #TensorCore #PTX #微架构 #Blackwell #CUDA

## Tensor Core 五代演进

| 代 | 年份 | 关键变化 |
|---|------|---------|
| Volta | 2017 | 4×4×4 MMA, warp 级 wmma, FP16→FP32 |
| Turing | 2018 | INT8/INT4 |
| Ampere | 2020 | TF32/BF16/FP64, 2:4 sparsity, cp.async |
| Hopper | 2022 | wgmma, TMA, FP8, mbarrier |
| Blackwell | 2025 | tcgen05, TMEM, 2SM MMA, FP4 |

## 设计趋势
1. 控制粒度：warp → warpgroup → CTA 单线程
2. 显式化：隐式寄存器 → TMEM 显式累加器管理
3. 异步化：所有数据搬运异步
4. 集群化：单 SM → SM pair → GPC → Cluster
5. 精度下探：FP32 → FP16 → BF16 → FP8 → FP4(Rubin)

## Blackwell 核心突破 (tcgen05)
- 单线程代表 CTA 发起 MMA，更大调度自由度
- TMEM：从"自动挡"到"手动挡"
- 2SM MMA：跨 SM Tensor Core 协作
- CLC + GPC Floorsweep：灵活 cluster 调度
- 代码已开源（SemiAnalysis PTX/SASS 微基准）

## 编译器影响
- CuTe 直接映射 PTX MMA atom，每代新 layout
- Triton Blackwell backend 需适配 tcgen05
- TVM/MLIR GPU dialect 需追踪 PTX 语义变化
