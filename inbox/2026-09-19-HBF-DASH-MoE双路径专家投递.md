# DASH：HBF 双路径直连 GPU，MoE 专家投递吞吐 +1.94×（arXiv 2608.14333）
- 来源: https://arxiv.org/abs/2608.14333
- 日期: 2026-09-19
- 标签: #HBF #MoE #AI芯片 #LLM推理 #内存架构

## 摘要
KAIST 提出 DASH：把 HBM 与 HBF 同时作为主存层，专家权重既走「GPU↔HBF 直连」也走「HBF→HBM base die→GPU 中转」，两路并发提升聚合投递带宽——不复制权重、也不引入共享中转瓶颈。

## 要点
- 路由粒度：以「整个专家」为单位分配两条路线并并发传输，规避权重复制与单路拥塞
- 延迟隐藏：early expert determination 提前判定将用到的专家，让 HBF 读延迟与前置计算重叠
- 干扰隔离：不可变专家权重与可变 KV Cache 分开管理，避免读写特性不同的两股流量互相拖累
- 结果（事件驱动 continuous-batching 模拟 + 实测 GPU 计算延迟）：代表性 MoE 负载下相对「全部经 HBM base die 中转」的单路设计，吞吐 1.94×、端到端 1.90×
- 定位差异：FLINT 把 HBF 做成只读权重层、HAVEN 做检索容量层，DASH 瞄准 MoE 稀疏专家投递，把 HBF 从容量扩展升为 GPU 内存系统一等公民
- 关联 [[HBF-NAND-AI推理内存2026]]
