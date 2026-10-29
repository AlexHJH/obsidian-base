# Ave：用数据流不变量给 agentic GPU 调优上护栏

- 来源: https://arxiv.org/abs/2604.18616 ｜ https://dl.acm.org/doi/10.1145/3830418.3843902
- 日期: 2026-04-16 v1 / 2026-09-14 v2（SOSP 2026）
- 标签: #MLSys #系统顶会 #Agent编译 #GPU调优 #DSL

## 摘要
LLM coding agent 能写出正确的 GPU kernel，但性能追不上手写库，根因是单测与 profile 只给稀疏的端到端反馈，agent 不知道自己违反了哪条全局约束（SMEM staging、软件流水、指令调度）。Ave 把 **data-flow invariant** 做成编译期护栏：tile-based Pythonic DSL 暴露硬件指令与编译策略，tag 函数给数据打符号标签，flow-sensitive 分析 + SMT 检查断言，违例返回具体反例，运行期零开销。

## 要点
- 闭环分两段：ICRL planner 从知识库提优化方案，lowering agent 落实并实例化所需不变量——与「agent + 编译器契约」路线一致。
- AMD MI300X 上 GEMM / flash attention / MoE（占推理 GPU 时间九成）达手写库有效吞吐 89–99%；KernelBench 200 题 Level 1 100%、Level 2 88% 三次内出合法 kernel。
- 明确限制：无法表达依赖堆上取值的 data-flow 不变量。
- 对本项目的迁移价值：验收契约不必是「跑通了」，可以是可静态检查的数据流性质 + 反例。
