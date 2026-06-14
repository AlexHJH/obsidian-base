# Blackwell Tensor Core 微架构逆向 — PTX/SASS 指令级剖析

- 来源: https://newsletter.semianalysis.com/p/dissecting-nvidia-blackwell-tensor
- 日期: 2026-04-01
- 作者: Kimbo Chen, Dylan Patel
- 标签: #Nvidia #Blackwell #TensorCore #PTX #SASS #微架构 #GPU #AI芯片

## 背景

数据中心 Blackwell GPU (SM100) 是 Nvidia 近十年来最大的一次 GPU 微架构变革，但官方至今没有发布详细白皮书。SemiAnalysis 耗时数月，对 Blackwell 进行了 PTX/SASS 指令级微基准测试，建立了实际性能上限与理论峰值的对比。**代码已开源。**

## 核心技术发现

### 1. tcgen05 — 新 MMA 指令范式
- Hopper 的 `wgmma` 由 warpgroup 内所有线程共同发起
- Blackwell 的 `tcgen05` 改为**单线程代表整个 CTA 发起**，CuTe MMA atom 使用 `ThrID = Layout<_1>`
- 意味着软件栈可以更灵活地编排计算与数据搬运的 overlap

### 2. Tensor Memory (TMEM) — 显式累加器管理
- 线程不再隐式持有 MMA 累加器结果
- TMEM 以 MMA scope 显式管理，带来更大的调度自由度
- 类比：从"自动挡"切换到"手动挡"，kernel 作者拥有更多控制权

### 3. 2SM MMA — 跨 SM 的 Tensor Core 协作
- 两个 SM 联合执行 MMA 操作，M 维度加倍
- 不同 SM 加载不同的矩阵 A 和 D 分片
- 突破单 SM 的 shared memory 和寄存器限制

### 4. TMA (Tensor Memory Accelerator) 增强
- `cp.async.bulk`：异步批量拷贝指令
- `LDGSTS`：异步 global→shared memory 拷贝
- mbarrier 完成同步机制
- FlashInfer 等推理库可直接受益

### 5. Cluster Launch Control (CLC) & GPC Floorsweep
- SM100 解决了 Hopper 的量化问题：kernel 可指定 preferred + fallback 两种 cluster size
- Floorsweep：某些 TPC 独占自己的逻辑 GPC，不与其它 TPC 共调度
- 影响 kernel 的 occupancy 和 wave 调度

### 6. Distributed Shared Memory
- 跨 SM 的分布式 shared memory 访问
- 降低跨 SM 数据搬运延迟

## 实操价值

- **ML 系统工程师**：理解 PTX 指令吞吐量上限，指导 kernel 优化方向
- **编译器开发者**：为 TVM/Triton/MLIR 的 Blackwell backend 提供 ground truth
- **芯片架构师**：揭示 Nvidia 在指令集抽象层的设计取舍

## 未来计划
SemiAnalysis 将持续扩展到：TPU Pallas kernels、Trainium NKI kernels、AMD CDNA4 assembly
