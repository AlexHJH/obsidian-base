# MLSys 2026 Oral：Meta Llama 推理部署配置优化
- 来源: https://mlsys.org/virtual/2026/oral/3780
- 日期: 2026-07-07
- 标签: #MLSys #LLM推理 #Meta #Llama #vLLM #部署优化

## 摘要
Meta 在 MLSys 2026 发表 Oral 论文，系统性分析 Llama 推理部署的数百万种配置组合，覆盖 H100/H200/MI300X 多种 GPU、张量/流水线/专家/上下文并行策略、continuous batching vs prefill-decode 分离等维度，目标在满足 SLO 前提下最大化吞吐。

## 要点
- Meta Llama 服务近 10 亿月活用户，推理部署需权衡硬件异构、并行策略、运行时设计
- 核心发现：并行策略有阶段特异性（prefill vs decode 不同最优配置），MoE 架构引入系统级新权衡
- scale-up（高带宽互联）vs scale-out（标准网络）对比：超大模型场景下 scale-up 未必经济
- vLLM 与 DeepLearning.AI 联合推出「Fast & Efficient LLM Inference」课程（2026-06），覆盖压缩→部署→基准测试全流程
- 关联 [[推理引擎格局-vLLM-SGLang]] [[MI355X推理优化案例]]
