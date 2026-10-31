# tcgen05 实战阶梯：手写 PTX 从 17% 爬到 cuBLAS 的 98%
- 来源: https://gau-nernst.github.io/tcgen05/
- 日期: 2025-12-21
- 标签: #NVIDIA #Blackwell #tcgen05 #TMA #PTX #cuBLAS

## 摘要
作者在 B200（sm100）上用纯 CUDA C++ + PTX 从零写 fp16 matmul，逐版本量化每一步优化收益：M=N=K=4096 下 v1 只有 255 TFLOPS，v6 达 1476 TFLOPS，对比 cuBLAS 1506（约 98%），并把「哪一步真正值钱」拆成可比数字。

## 要点
- 阶梯：v1 2D/16B TMA 255 → v2 改 128B TMA 681（2.7x，最大一跳）→ v3 流水 940 → v4 warp specialization 1209 → v5 2-SM MMA 1302 → v6 persistent+静态调度 1476
- 128B swizzle 的收益本质是「tile 变宽（16B→128B）」，代码里只需 tensor map 设 128B 并在 shared descriptor 置 bits 61-63=2，不必手算 swizzle；3D TMA 单次 issuance 反而略慢（253/695），说明不是所有布局都吃得到 TMA 引擎
- 同步只有两对生产者-消费者（TMA↔MMA 用 smem，mainloop↔epilogue 用 TMEM），都用 mbarrier；v6 的关键是 148 个 DC 固定 block 常驻 + 6 warp 分工（1 TMA / 1 MMA / 4 epilogue，4 个是访问满 TMEM 的下限）
- 2-SM（`.cta_group::2`）只涨 8%：descriptor 仍传本地 smem 地址，硬件按相同 offset 去读远端 CTA 的 smem，因此 cluster 内各 rank 的对象布局必须完全一致；集群级 `__syncthreads` 是 `barrier.cluster.arrive/wait`
- 观察：Blackwell 上 Tensor Core 编程可以只在 tile 粒度思考（无需 thread 级地址/布局推算）；难点转移到 generic proxy 与 async proxy 的同步成本，作者指向 FA4 作为该问题的案例
- 口径：Modal B200 单机、单 shape、PyTorch 2.9.1+cu13 的 cuBLAS 基线；无 ncu，改用 kernel 内 clock 采样导 Perfetto trace 定位瓶颈

关联：[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[2026-10-28-NVIDIA-Rubin-SM107-CUTLASS48与PTX94]]
