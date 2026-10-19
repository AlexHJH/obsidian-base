# AI as a Compiler：让 LLM 直接吐 PTX，绕过 Triton 后端（0.83×–3.34×）
- 来源: https://arxiv.org/abs/2609.36800
- 日期: 2026-09-29
- 标签: #AI编译器 #Triton #PTX #GPU #LLM代码生成

## 摘要
Stanford/EPFL（F. Costa、C. Castes、T. Bourgeat、A. Mirhoseini）提出 "AI lowering"：不要多级 IR 与手写 lowering，让 LLM agent 把 Triton kernel 直接翻译成 PTX，用数值+性能反馈迭代，可选接形式化验证。在 Ada/Hopper/Blackwell 三代卡、12 个常见 kernel + 10 个近两年论文里的 kernel 上，达到 autotuned Triton 的 0.83×–3.34×。作者用 GPT-6 Astra——在此之前 GPT-5.4 只有 0.73×、GPT-5.6 Sol 0.97×，只有最新前沿模型才刚够打平。

## 要点
- 收益来自 Triton lowering 根本不做的事：BitDelta 把打包二进制权重直接解到 Tensor Core operand（3.34×）、FlashAttention 让每线程独占一条完整 softmax 行对齐 tensor memory（1.37×）、卷积复用重叠输入窗口（2.23×）
- 生成代码真的用了各代硬件机制：Ada 的 mma.sync+ldmatrix+cp.async、Hopper 的 wgmma+TMA+mbarrier、Blackwell 的 tcgen05+TMEM+二维 TMA，SMEM swizzle 也从 XOR 走到 128B/64B offset
- 反「背答案」实验做得扎实：随机生成的 ~150 行无语义整数 kernel 全部正确；故意给误导线索（名叫 ReLU 实际要 GELU、注释与实现不符）也跟实现走，不跟名字走
- 代价在验证：Hopper/Blackwell 的 tcgen05/TMEM/异步语义超出原 Volta 验证器范围，作者扩了模拟器才补上；这是全文最脆的一环
- 小算子基本只打平（激活函数 ±1%、RMSNorm ≤4%），真正拉开差距的是 GEMM/卷积/attention 这类结构可变的算子——对 [[AI编译器2026格局]] 里「IR 是护城河」的判断是反证
- 关联 [[AI编译器2026格局]]、[[Nvidia-GPU微架构演进]]、[[MLSys2026论文精选]]
