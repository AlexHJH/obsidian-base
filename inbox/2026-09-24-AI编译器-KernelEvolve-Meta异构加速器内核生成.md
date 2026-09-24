# KernelEvolve：Meta 把内核生成做成 Agent 框架，Triton 成异构硬件的收敛点
- 来源: https://arxiv.org/html/2512.23236v4
- 日期: 2026（arXiv 2512.23236v4，v1 2025-12）
- 标签: #AI编译器 #Kernel生成 #Triton #Agent #Meta

## 摘要
Meta 的 KernelEvolve 用 agent 框架为广告排序（DLRM）训练/推理在异构加速器上自动生成并优化高性能内核，输入是 kernel 规格、输出是可直接部署的实现。论文把「编程模型碎片化」列为首要障碍：CUDA 线程块模型、Triton tile DSL、ROCm/HIP、CuTe layout 代数、MTIA 的 C++ DSL 在语法之下是根本不同的执行模型。

## 要点
- 以 Triton 为主要目标：论文统计 Triton 已成主导 DSL（使用量增长 60%），跨 NVIDIA/AMD/MTIA 均可落地，同时保留 CuTe DSL、HIP、FlyDSL 等低层通道
- 覆盖 DLRM 训推全流程的 kernel 自动生成 + 优化，属于「在生产规模异构 fleet 上跑 agentic kernel coding」而非单卡 benchmark
- 与既有路线的关系：KernelPro/KernelAgent 一类闭环搜索负责算法与调优，编译器负责 layout/同步/后端 lowering——agent 选算法、编译器保正确性与移植
- 与 [[inbox/2026-09-22-AI编译器-TIRx硬件原生内核DSL与Agentic内核编程]] 同向：内核编程正在从「人写 DSL」变为「agent 写 + 编译器担保」
- 解读信号：MLIR/Triton 是 agent 的稳定接口层，真正稀缺的是「不撒谎的度量」（benchmark + 验证），见 [[inbox/2026-09-22-AI编译器-LLM生成GPU内核真实覆盖率与KernelBench评分漏洞]]
- 关联 [[AI编译器2026格局]] [[MLSys2026论文精选]]
