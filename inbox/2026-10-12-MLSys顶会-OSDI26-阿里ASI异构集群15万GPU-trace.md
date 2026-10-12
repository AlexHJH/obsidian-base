# OSDI'26：阿里 ASI 六个月 15.5 万 GPU trace，高需求 ≠ 高有效利用率
- 来源: https://www.usenix.org/conference/osdi26/presentation/li-suyi
- 日期: 2026-07-13
- 标签: #MLSys顶会 #OSDI #集群调度 #GPU利用率

## 摘要
OSDI'26 Operational Systems 论文刻画阿里 Serverless Infrastructure（ASI）：六个月 trace 覆盖 155,410 块多厂商多代 GPU、81 个部门的 ad-hoc 开发 / 训练 / 在线与离线推理。核心结论是 GPU 需求高不等于有效利用率高（HKUST + 阿里 + 复旦）。

## 要点
- 不可分配的原因不是没卡：空闲容量碎散在各节点、缺配套 CPU、违反网络局部性，且用户为生产安全预留大量 headroom；以往热议的小数（fractional）GPU 碎片化如今已可忽略——GPU 共享很少被使用
- 已落地解法：GPU defragmentation 算法把「存在闲置资源的节点数」砍掉 20.2%；SpotGPU 抢占成本感知调度安全收割闲置资源，把 GPU 分配率从 68% 提到 93%
- 开放挑战：多厂商 GPU 采用偏斜、异构 GPU 之间的带宽瓶颈、共置负载干扰；ASI trace 已公开发布（称规模与负载多样性迄今最全）
- 关联 [[MLSys2026论文精选]]、[[AI芯片推理硬件2026全景]]
