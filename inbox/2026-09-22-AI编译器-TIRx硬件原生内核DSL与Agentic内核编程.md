# TIRx：TVM 推出硬件原生 kernel DSL，把编译边界压低到 TileLang 之下
- 来源: https://tvm.apache.org/2026/06/22/tirx
- 日期: 2026-06-22
- 标签: #TVM #AI编译器 #MLIR #megakernel #AgenticKernel

## 摘要
Apache TVM 社区发布 TIRx：一个硬件原生的 kernel DSL 与编译器（`pip install apache-tvm` 0.25.0 起）。它对标 Triton（隐藏线程分配与布局）与 TileLang（暴露内存层级但仍交布局推断给编译器）之后更低的边界——把编排留在源码里，把可复用 tile 原语交回编译器。

## 要点
- 三条设计决策：① 流水结构/同步/角色分配/内存摆放/后端 intrinsic 留在硬件原生源码，不做抽象 ② 重复出现的 tile 原语（执行域、tensor layout、分派）暴露给编译器，跨后端可移植 ③ 新硬件先以 intrinsic 进入，用法稳定后再升格为 tile 原语
- 刻意不把重优化放进必经路径：自动 warp specialization、布局推断、调度变换、自动调优都可以作为**可选上层**，核心 DSL 不依赖它们也能表示新程序；正与 TileLang 社区合作把 TIRx 作为其最小底座
- B200（SM100）54 组配置实测：BF16 8192³ 1517 TFLOPS（0.96× DeepGEMM）、FP16 1404、FP8 4096×4096×7168 2895 TFLOPS（0.99×）、NVFP4 8192³ 5930 TFLOPS（差最强基线 2%）、FA4 长序列与 CuTeDSL 的 flashattn_sm100 基本打平（0.99–1.00×）
- megakernel 方向：kernel 不再以不透明边界交付，而是以**设备侧任务（tile task）**暴露，输入输出/内存域/layout/同步/执行归属都可被上层编译器改写（重算 shared memory 偏移、重命名 barrier、重分配 warp 角色）；已在其上做出 Event Tensor（MLSys'26, arXiv 2604.13327）
- agentic kernel programming：IR 对象与 pass 通过 TVM FFI 暴露给 Python/C++/Rust，方便 agent 快速做侵入式分析、合法性检查与 IR 变异；并给出 L1 局部调优 → L2 从人工搜索空间采样 → L3 由 meta-rule 生成搜索空间 → L4 从硬件文档自举搜索空间的能力阶梯
- 核心论点：agent 优化的瓶颈是奖励信号稀疏，结构化的 TIRx 程序能中途给出「是否良构 / 同步是否有效 / 是否无竞争 / 模拟值是否匹配」等稠密反馈，而不必等到编译-跑测-打分
- 关联 [[AI编译器2026格局]] [[MLSys2026论文精选]] [[Nvidia-GPU微架构演进]]
