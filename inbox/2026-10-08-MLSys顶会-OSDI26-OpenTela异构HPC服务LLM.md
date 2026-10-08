# OSDI'26：OpenTela 把碎片化 HPC 集群拼成异构 LLM 服务平台
- 来源: https://www.usenix.org/conference/osdi26/presentation/yao ; https://systems.ethz.ch/news-and-events/news/2026/03/two-papers-accepted-at-osdi26.html
- 日期: 2026-10-08
- 标签: #MLSys顶会 #OSDI #LLM推理 #系统

## 摘要
OSDI'26（7/13–15，Seattle）接收率 20%（136/681）。ETH Zurich 的 OpenTela 提出用户态编排 overlay，把分散、异构、由 Slurm 管理的 HPC 集群统一成跨机构 LLM 服务台：CRDT gossip 容错服务发现、统一 API 屏蔽各集群管理器与推理引擎差异、异构感知调度，无需 root、不改集群配置。

## 要点
- 已实际部署 22 个月、服务 1300 万请求——系统顶会里罕见的长期生产数据，可信度显著高于纯仿真工作
- 要解决的约束：HPC 里没有 K8s 的 service discovery/routing/health，分配是临时的、节点对外不可达、GPU 容量跨管理域碎片化
- 同期 ETH 另一篇 RoCE BALBOA：面向数据中心 SmartNIC 的 service-enhanced RDMA offload 引擎（研究 track）
- 关联 [[MLSys2026论文精选]]、[[推理引擎格局-vLLM-SGLang]]
