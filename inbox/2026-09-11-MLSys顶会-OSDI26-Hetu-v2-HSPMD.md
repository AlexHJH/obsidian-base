# OSDI'26｜Hetu v2：用「分层异构 SPMD 标注」统一异构训练
- 来源: https://www.usenix.org/system/files/osdi26-li-haoyang.pdf
- 日期: 2026-09-11
- 标签: #MLSys #OSDI #SPMD #分布式训练

## 摘要
北大/上交/普渡的 Hetu v2 提出 HSPMD（Hierarchical & Heterogeneous SPMD）：把 SPMD 的声明式标注下沉扩展为非对称分片（asymmetric sharding）与分层通信原语组合，用同一套单设备视角的编程模型表达异构并行，直击大规模训练的「空间异构」（设备不齐/故障）与「时间异构」（负载随数据变化）。

## 要点
- 路线选择：不堆调度器层，而是把扩展性推到 primitive 层（声明式标注本身），延续 SPMD「并行策略与单设备逻辑分离」的优点
- 空间异构：渐进式图特化（progressive graph specialization），为不同设备生成各自执行逻辑
- 时间异构：动态策略切换，训练中按需改变分片/通信策略
- 对 GP+CC 异构 SoC 有直接参考价值：非对称分片 = 不同核心承担不同 shard 尺寸
- 关联: [[MLSys2026论文精选]] [[推理引擎格局-vLLM-SGLang]]
