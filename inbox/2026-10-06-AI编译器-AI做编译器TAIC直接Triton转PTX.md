# AI as a Compiler：让 LLM 直接做 lowering，Triton 转 PTX 绕开传统流水线
- 来源: https://arxiv.org/abs/2609.36800
- 日期: 2026-09-29
- 标签: #AI编译器 #Triton #LLM #形式化验证

## 摘要
Stanford（Francois Costa、Azalia Mirhoseini）+ EPFL 提出「AI lowering」：LLM 把 Triton 源码直接综合成 PTX（系统名 TAIC），不经 Triton 自有的多级 IR 与手写优化 pass。作者用 TCEnv 负责执行与验证、把 NCU 指标/PTX ISA 检索/GPU 优化原则作为三项通用技能挂给 LLM，在 Ada/Hopper/Blackwell 上跑 12 个常见 kernel + 10 个近期论文 kernel，性能为 autotuned Triton 的 0.83×–3.34×。

## 要点
- 最大收益来自 Triton lowering 根本不做的变换：packed binary weight 直接解码进 Tensor Core 操作数（BitDelta 3.34×）、softmax 整行交给单线程放 tensor memory（FlashAttention 1.37×）、复用重叠卷积
- 不是全面替代：区间下界 0.83× 说明仍有 kernel 慢于 autotuned Triton；12 个常见 kernel 中有 2 个（reduction sum、softmax）连「无约束 PTX 的可验证性」都过不了
- 验证独立于生成：刻意不把验证器放进生成回路去约束输出，而是事后验证无约束 PTX——与本库「验证检疫必须独立于提交者」同构
- 可验证性边界：常见 kernel 套件除归约与 softmax 外全部可验；10 个近期 kernel 中 6 个通过（FlashAttention、Forgetting Attention、SageAttention、Lion、Mamba-2 两个原语）
- 判据：AI 编译器的竞争点从「生成 kernel」转向「生成 + 验收」配对，跨 SM 归约与数值稳定性是当前最硬的边界
- 关联 [[AI编译器2026格局]]、[[MLSys2026论文精选]]
