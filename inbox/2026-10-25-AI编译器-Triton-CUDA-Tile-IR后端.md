# Triton 的 CUDA Tile IR 后端：绕开 SIMT lowering，直接编到 Tile 级 ISA

- 来源: https://github.com/triton-lang/Triton-to-tile-IR ; https://developer.nvidia.com/blog/advancing-gpu-programming-with-the-cuda-tile-ir-backend-for-openai-triton
- 日期: 2026-10（仓库与调优文档均已更新至 CUDA 13.4 口径）
- 标签: #AI编译器 #Triton #MLIR #CUDA-Tile #后端

## 摘要

Triton-to-TileIR 是 triton-lang 组织下的孵化仓，为 Triton 增加一个以 CUDA Tile IR 为目标的第三后端。与 PTX 后端不同，它不把 tile 级抽象拆成线程级 SIMT 代码，而是保留 tile 语义直接下沉到 CUDA Tile IR（MLIR 方言 + 字节码），由 tileiras/PTXAS 完成剩余映射。开关是 `ENABLE_TILE=1`，可按 kernel 在 PTX 与 TileIR 之间二选一。

## 要点

- 依赖栈已切到 CUDA 13.4（13.4.59）的 `tileiras`/`ptxas`/`libnvvm.so`，分支基于 Triton 3.7，验证平台是 B200——后端可用性被绑在 CUDA 版本的兼容区间上（如 v13.1.1→v13.1.3 这种按 LLVM 破坏性提交切段）
- 调优语义变了：`num_warps` 仅为兼容参数，真正决定性能的是 `occupancy`（每 SM 预期活跃 block 数）与 `num_ctas`——tile 级后端把「线程怎么排」的旋钮换成了「一个 SM 上放几个块」
- 数值策略默认更保守：TileIR 后端默认关闭 approx 与 ftz，需 `TILEIR_ENABLE_APPROX=1` / `TILEIR_ENABLE_FTZ=1` 才换回 attention 类 kernel 的性能；这等于把精度-性能权衡从编译器默认值交还给用户
- 内存模型是主要未收口处：已对别名访问、descriptor reduction 实现保守的 token 插入，但用户直接控制 memory token 仍是开放 API 议题；单 tile block 内的定序不能替代 splitK/streamK 与锁协议所需的跨 block 同步
- 未覆盖面诚实：并非全部 Triton 算子已实现，tensor-of-pointer（指针张量）路径存在性能退化——「能跑」与「能生产」之间仍隔着一张算子清单
- 生态信号：Helion 已提供 `HELION_BACKEND=tileir`（要求 Blackwell cc10.x/12.x）；NVIDIA 的定位是「你有你自己的 DSL，我提供 Tile ISA 作为稳定目标」，与 Gluon 的显式控制路线形成两条并行下沉路径
- 口径：仓库状态与调优参数取自 README / PerformanceTuningTips 文档，未实测跑分；博客原文发布于 2026-01，本文采信的是其 CUDA 13.4 更新后的内容

## 关联

[[AI编译器2026格局]]、[[Nvidia-GPU微架构演进]]
