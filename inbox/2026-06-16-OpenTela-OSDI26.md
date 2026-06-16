# OpenTela: 去中心化HPC集群统一LLM推理 — OSDI'26
- 来源: https://systems.ethz.ch/news-and-events/news/2026/03/two-papers-accepted-at-osdi26.html
- 日期: 2026-06-16
- 标签: #OSDI #LLM推理 #分布式系统 #HPC #去中心化

## 摘要
ETH Zurich 团队在 OSDI'26 发表 OpenTela，提出基于 P2P 网络的去中心化计算架构，将异构 HPC 集群统一为 LLM 推理资源池。已开源并用于 SwissAI 生产环境。

## 要点
- P2P gossip 协议替代中心调度器，网络分区下仍维持全局资源视图
- CRDT 实现状态管理，支持异构 GPU 混合推理
- 已开源 (github.com/eth-easl/opentela)，Apache 2.0 许可
- 作者含 ETH/Cambridge/MIT/EPFL/HKUST 多机构联合
