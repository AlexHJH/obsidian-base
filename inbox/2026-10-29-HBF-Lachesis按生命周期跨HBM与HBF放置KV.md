# Lachesis：按生命周期把 KV 跨 HBM/HBF 放置

- 来源: https://arxiv.org/abs/2610.08378
- 日期: 2026-10-06
- 标签: #HBF #KV-Cache #Agent推理 #内存层级 #耐久性

## 摘要
Agent 负载让 KV 随 harness 与子 agent 累积并被反复复用，HBF 容量是 HBM 的十倍级、读带宽接近 HBM，但**写寿命有限**才是真瓶颈。Lachesis 在 agent harness 与推理引擎之间做一层放置决策：按每段 KV 的「生命周期」决定落 HBM 还是 HBF，段不再被读时立即释放块，用 HBM 吸收短命写入。

## 要点
- 生命周期发散三轴：时间（前缀复用窗口）、结构（agent/子 agent 树形层级）、跨 worker（并行 worker 共享上下文）——均由 harness 编排决定，而非模型或引擎决定。
- 实测为 trace 驱动模拟：相比 HBM-first 放置，HBF 寿命延长 1.19–3.13×，落到 3.3–12.2 device-years；多 agent trace 在 24×7 满载紧 SLO 下仍超五年质保。
- 与 H3 / FLINT / FlashAccel / DASH 的区别：那些按「只读权重」或读写比挑数据，本文把 **agent 语义生命周期**当作第一类信号。
- 架构含义：harness 的设计选择（何时开子 agent、上下文如何回收）直接决定存储耐久性，属于跨层契约而非纯引擎调优。
