# Triton DSL 内核性能评估差距 & KernelPro LLM 优化系统
- 来源: https://arxiv.org/abs/2607.04454 & https://arxiv.org/abs/2606.26453
- 日期: 2026-07-15
- 标签: #AI编译器 #Triton #GPU #LLM #内核优化

## 摘要
两篇互补论文：一篇揭示了 Triton/TileLang DSL 内核"正确但慢"的问题——仅做正确性验证可漏掉最高 300x 性能差距，并提出轻量级筛选标准；另一篇提出 KernelPro，用 LLM + 微剖析工具闭环迭代优化 GPU 内核，在 KernelBench 全三级达 SOTA，首次同时优化能耗。

## 要点
- Triton DSL 评估：22 个内核在 A100/GH200 上测试，TileLang LayerNorm 内核正确但比 PyTorch 慢 300x+
- 失效原因分两类：可修复的编写缺陷（顺序规约、dtype 转换）vs 代码生成/自调优覆盖的结构性残余
- 两项轻量检查（库相对效率 + Roofline 利用率）可全覆盖筛选低效内核
- KernelPro：LLM 多智能体 + NCU/SASS/NSYS 剖析反馈 + MCTS 搜索，首次生成 CuTe 源码级优化
- KernelBench L1/L2/L3 分别 2.42x/4.69x/5.30x 加速，VeOmni MoE 内核超手工 Triton 1.23x
- 首次实现 GPU 内核优化能耗兼顾：等速下实测节能 11.6%
