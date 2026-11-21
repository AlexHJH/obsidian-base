# SOSP 2026 两篇 GPU 内核工作：Ave 给 agent 加不变量护栏，MeldKernel 把融合边界推到全 GPU
- 来源: https://arxiv.org/abs/2604.18616 （Ave）、https://dl.acm.org/doi/pdf/10.1145/3830418.3843857 （MeldKernel）；会议 https://sigops.org/s/conferences/sosp/2026/accepted.html
- 日期: 2026-09-28
- 标签: #SOSP2026 #AI编译器 #GPU #Agent #KernelFusion

## 摘要
SOSP'26（布拉格，62/390 = 15.9%）内核侧的两条主线：Ave 用「数据流不变量」当编译期护栏，让 LLM agent 优化 GPU kernel 时不必靠反复实测纠错；MeldKernel 把 kernel fusion 从 SM 内推到跨 SM，专治 decode attention 与 MoE 路由这类运行时才知道形状的动态负载。

## 要点
- Ave（CausalFlow/HKUST/UCAS/THU/Stanford）：tile 级 Pythonic DSL 暴露硬件指令与调度策略但隐藏 layout 编码；tag 函数给数据打符号标签，编译器沿数据流传播，tag 断言用 SMT 在编译期检查，返回具体违例赋值当修复指引，运行时零开销
- Ave 实测（AMD MI300X，GEMM + flash attention + MoE，约占 LLM 推理 GPU 时间 90%）：配 GPT-5.6 Sol 达手写库 89–99% 有效吞吐；KernelBench 200 题三次内出有效 kernel 的比例 L1 100% / L2 88%
- MeldKernel（SJTU IPADS）：把粗粒度 phase 级依赖拆成 sub-task 依赖，用片上互连做细粒度 SM 协同 + 异步 just-in-time reduction，减少中间结果落 off-chip 的流量；关键负载平均 1.3×、最高 1.4×
- 判读：Ave 的价值不在「agent 会写 kernel」，而在把正确性约束变成可判定的编译期对象——这正好压住 agent 优化最容易出的「跑得快但算错」；MeldKernel 则说明动态形状的融合收益来自调度自由度而非编译期展开
- 口径：Ave 的 1.62–1176× 速度上界是相对「未净化 agentic 基线」，上界之高来自基线的失败与未优化，看 89–99% 手写库那一组更有意义；两篇都在特定 SKU（MI300X / NVIDIA 某代）上测，跨架构不外推
- 关联 [[AI编译器2026格局]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[MLSys2026论文精选]]
