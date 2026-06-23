# OpenTela(LLMFabric)：统一去中心化 HPC 集群的异构 LLM 服务
- 来源: https://www.usenix.org/conference/osdi26/presentation/yao
- 日期: 2026-06-23
- 标签: #MLSys #OSDI #系统顶会 #LLM推理 #异构计算 #去中心化

## 摘要
ETH Zurich 等团队 OSDI'26 论文。针对"主权 AI"需求——在自控 HPC 集群上服务 LLM，但 HPC 环境缺少 Kubernetes 式控制面（Slurm 调度、节点不可外联、算力跨域碎片化）。OpenTela 在用户态提供容错服务发现、统一 API 与异构感知调度，无需 root 或集群重构。

## 要点
- 痛点：现有推理引擎靠外部控制面做服务发现/路由/负载均衡，HPC(Slurm)缺乏这些原语，算力又跨异构集群碎片化
- 方案：CRDT gossip 网络做容错服务发现 + 统一 API 适配多调度器与推理引擎 + 异构感知调度器，全程用户态无特权
- 作者跨 ETH/Cambridge/EPFL/MIT/HKUST；USENIX 页标题为 LLMFabric，PDF 为 OpenTela
- 同组 RoCE BALBOA(OSDI 研究轨)：SmartNIC 上的 RDMA 卸载引擎，增强数据中心网络服务
