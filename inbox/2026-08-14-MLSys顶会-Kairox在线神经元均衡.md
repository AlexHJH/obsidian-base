# Kairox：在线神经元均衡的 GPU-CPU 混合 LLM 推理（OSDI'26）
- 来源: https://www.usenix.org/conference/osdi26/technical-sessions
- 日期: 2026-07-13
- 标签: #MLSys #OSDI26 #LLM推理 #GPU-CPU混合 #稀疏

## 摘要
SYSU×鹏城实验室×EPFL 提出 Kairox，自适应 GPU-CPU 混合推理系统：针对静态"热/冷"神经元划分无法适配运行时激活变化的缺陷，用在线神经元均衡按激活模式动态在 GPU/CPU 间重分布 FFN 神经元，缓解 CPU 算力瓶颈。

## 要点
- Live Pipeline 预取神经元（预测下一层激活模式）；Temporal Activation Momentum 缓存策略优先保留持续活跃神经元
- 相比静态划分，动态重分布提升混合推理吞吐
- 关联 [[MLSys2026论文精选]]
