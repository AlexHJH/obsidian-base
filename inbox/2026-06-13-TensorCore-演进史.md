# Nvidia Tensor Core 演进 — Volta → Blackwell

- 来源: https://newsletter.semianalysis.com/p/nvidia-tensor-core-evolution-from-volta-to-blackwell
- 日期: 2026 年
- 标签: #Nvidia #TensorCore #GPU架构 #PTX #AI编译器 #CUDA

## 摘要

系统梳理 Nvidia Tensor Core 从 Volta (2017) 到 Blackwell (2025) 八年间在 PTX 指令、数据布局、MMA 语义、数据搬运四个维度的演进。对于 AI 编译器后端开发者是必读参考。

## 各代核心变化

### Volta (2017) — 开山之作
- 首次引入 Tensor Core：4×4×4 矩阵乘加（FP16 输入，FP32 累加）
- 线程通过 `wmma` 指令以 warp 为单位协作
- 数据和线程布局采用交错模式（interleaved pattern）
- 编程复杂度高，CUTLASS 封装了大量底层细节

### Turing (2018) — 精度扩展
- 新增 INT8、INT4 Tensor Core
- 仍以 warp 为单位，架构与 Volta 兼容

### Ampere (2020) — 性能翻倍
- 新增 TF32、BF16、FP64 Tensor Core
- 支持稀疏结构化剪枝（2:4 sparsity）
- 异步拷贝指令 `cp.async` 引入，shared memory 预取成为主流模式

### Hopper (2022) — warpgroup MMA
- `wgmma` 指令：warpgroup 级别的矩阵乘
- TMA (Tensor Memory Accelerator) 硬件单元：异步 global→shared 搬运
- `cp.async.bulk`：批量异步拷贝
- mbarrier：细粒度同步原语
- FP8 (E4M3/E5M2) 支持，Transformer Engine 的核心

### Blackwell (2025) — tcgen05 范式
- `tcgen05`：改为单线程代表 CTA 发起 MMA
- TMEM：显式管理的累加器内存
- 2SM MMA：跨 SM 的 Tensor Core 协作
- CLC (Cluster Launch Control)：灵活 cluster size
- PDL (Programmatic Dependent Launch)：动态 kernel launch

## 设计趋势

1. **控制粒度越来越粗**：warp → warpgroup → CTA 单线程
2. **显式化**：隐式寄存器累加器 → TMEM 显式管理
3. **异步化**：所有数据搬运都是异步的
4. **集群化**：单 SM → SM pair (2SM MMA) → GPC → Cluster
5. **精度持续下探**：FP32 → FP16 → BF16 → FP8 → FP4 (Rubin)

## 编译器和框架影响

- CuTe (CUTLASS 3.x) 直接映射到 PTX MMA atom，每代架构变化意味着新的 atom layout
- Triton 的 Blackwell backend 需要适配 tcgen05 的新语义
- TVM/MLIR 的 GPU dialect 需要追踪这些 PTX 层面的语义变化
