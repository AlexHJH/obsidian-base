# TAIC：让 LLM 直接吐 PTX，绕过 Triton 的整条 lowering 管线
- 来源: https://arxiv.org/abs/2609.36800
- 日期: 2026-09-29
- 标签: #AI编译器 #Triton #PTX #AI芯片 #形式化验证

## 摘要
Stanford/EPFL（Costa、Castes、Bourgeat、Mirhoseini）提出「AI lowering」：固定 Triton kernel 的编译契约 (c, I, h)，让 LLM 直接生成 PTX，用数值差分测试 + profiler 反馈迭代，不走 IR 逐级 lowering。十二个常用 kernel 上达到 autotuned Triton 的 0.83×–2.23×（FP16 GEMM 在 H100 上只有 0.83×）；十个近期论文 kernel 里 BitDelta 3.34×/2.51×、FlashSinkhorn 1.55×、SageAttention 1.41×、FlashAttention 1.37×。

## 要点
- 增益来自 Triton lowering 做不到的变换：把 packed 二值权重直接解到 Tensor Core 操作数（BitDelta）、每个线程独占一整行 softmax 放进 tensor memory（FA）、复用重叠卷积窗口（最高 2.23×）；逐元素算子只回到 1% 内，说明多数 kernel 只是「恢复基线」
- 算力换来的：逐元素候选 <$1.50，卷积/GEMM $5–$12；5 次重复的方差不可忽略（B200 FP8 卷积 2.004±0.197，FA 1.236±0.106）——比 Triton 毫秒级编译贵几个数量级，靠 kernel 反复执行摊销
- 模型档位是硬门槛：同一协议下 FP16 GEMM 上 GPT-5.4 只有 0.73×、5.6 Terra 0.87×、5.6 Sol 0.97×，首个与 Triton 打平的模型才被选作主实验——说明能力阈值卡在「最新前沿模型」
- 验证是真瓶颈：把 Volta（PTX 符号执行等价检查）放进回路后，GEMM 掉到 0.24×、FA 0.78×——现代 GPU 的性能特性大半不在其语义覆盖内；扩展 Volta 支持 tcgen05/TMA/mbarrier/proxy fence 后 TCB 从 42 kLoC 涨到 60 kLoC，仍有 atomics、输入相关控制流、位级操作三类支持不了（reduction sum、softmax 无法形式化验证）
- 生成代码用了 100+ 个 Triton 编译器从不产出的 PTX 指令变体 → 生成能力已跑在验证能力之前，作者把这定为 AI kernel 生成的中心问题
- 对自研后端/Triton→RISC-V 的含义：如果 PTX 这一层能用「生成 + 测量 + 验证」替代手写 lowering，矩阵扩展的 bring-up 成本逻辑上可被压缩；但前提是目标 ISA 有可符号执行的语义模型，而 RISC-V 矩阵扩展恰恰缺这个
- 口径：单次运行主结果、成本为 API 计费累计值；Volta 扩展不保证 soundness（每多建模一条指令多一份不健全风险）
- 关联 [[AI编译器2026格局]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
