# Ave（SOSP'26）：用数据流不变量当编译期护栏，管住 agent 优化 GPU 内核
- 来源: https://arxiv.org/abs/2604.18616 （SOSP'26 录用: https://sigops.org/s/conferences/sosp/2026/accepted.html）
- 日期: 2026-09-14
- 标签: #SOSP #AIAgent #GPU内核 #形式化验证 #AI编译器

## 摘要
Ave 认为 LLM agent 已经能写出"正确"的 GPU kernel，卡点是测不出「违反了哪条全局约束」——单元测试与 profile 只给稀疏的端到端反馈。它把数据流不变量做成编译期护栏：tag 函数给数据打符号标签，编译器沿数据流与控制流传播，在使用点用断言校验，SMT 求解器给出具体反例，运行期零开销。

## 要点
- 三个角色分工：ICRL 规划器从 GPU 优化知识库里挑优化项 → lowering agent 实现并实例化所需不变量 → 编译器静态校验，违反就返回反例而非只报失败
- DSL 是 tile 级 Pythonic 文风，暴露硬件指令与编译器策略（shared memory staging、软件流水、指令调度），同时隐藏复杂内存布局，兼顾「LLM 可学」与表达力
- 分析为流敏感、路径不敏感 + SMT 求解，可静态判定 tag 断言，因此护栏不增加运行时成本
- 实测（AMD MI300X，GEMM/flash attention/MoE，合计占 LLM 推理 GPU 时间 90%）：GPT-5.6 Sol 下达手工优化库有效吞吐的 89–99%，几何均值比未经污染的 agentic 基线高 1.62–1176×
- KernelBench 200 题：Level 1 100%、Level 2 88% 能在三次尝试内产出有效 kernel
- 意义：与 [[MLSys2026论文精选]] 里 MLSys'26 keynote 的「zero trust」同向——不指望 agent 守规矩，而是让约束可被机器独立判定
- 关联 [[MLSys2026论文精选]] [[AI编译器2026格局]] [[AI-Agent-2026格局]]
