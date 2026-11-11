# AI lowering：让 LLM 直接吐 PTX，跳过整个 IR 栈
- 来源: https://arxiv.org/abs/2609.36800（"AI as a Compiler", Costa/Castes/Bourgeat/Mirhoseini, EPFL）
- 日期: 2026-09-29
- 标签: #Triton #PTX #AI编译器 #LLM-for-Compiler #形式化验证

## 摘要
论文提出「AI lowering」：不给 Triton 写后端，让 LLM agent 把 Triton kernel 直接翻译成 PTX，再用一个能验证候选 PTX 的环境做搜索/筛选。在 Ada/Hopper/Blackwell 三个代际的 12 个常见 kernel 与近期论文的 10 个 kernel 上，相对 autotuned Triton 达到 0.83×–3.34×。

## 要点
- 收益来自 Triton lowering 管线根本不做的变换：把 packed binary weights 直接解码进 Tensor Core operand（BitDelta 3.34×）、每线程独占一整行 softmax 放进 tensor memory（FlashAttention 1.37×）、复用重叠卷积窗口（最高 2.23×）
- 代价核心不在生成而在验证：作者在 Volta（已有 PTX verifier）上扩出 Blackwell `tcgen05` 支持，需要建模三件事——managed tensor memory、descriptor 式 operand 布局、以及由 commit/wait/mbarrier/proxy fence 编排的异步执行
- 数据要按区间读：0.83× 说明有 kernel 是输的，论文没有承诺普适加速；22 个 kernel 的样本也撑不起「换掉编译器」的结论
- 对自研芯片的真正含义：新目标只需提供一个 verifier + 执行环境，不必先写一套 IR 与 lowering pass；难点从「写编译器」移到「能不能形式化描述这台机器的执行模型」
- 与 [[AI编译器2026格局]] 的走向对照：Triton 3.9 在把扩展性做进 pass pipeline（见 [[2026-11-11-AI编译器-Triton3.9-CLC与Gluon下探]]），这篇主张把 pipeline 本身交给模型

关联：[[AI编译器2026格局]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[RISC-V矩阵扩展两年进展-2024-2026]]
