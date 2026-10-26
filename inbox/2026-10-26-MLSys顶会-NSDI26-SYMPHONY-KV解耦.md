# SYMPHONY：把 KV cache 从计算节点剥离（NSDI'26）
- 来源: https://www.usenix.org/conference/nsdi26/presentation/agarwal（预印本 arXiv:2412.16434）
- 日期: 2026-05-04
- 标签: #NSDI #LLM推理 #KVcache #内存解耦

## 摘要
UT-Austin + UW-Madison 提出的 disaggregated memory 管理层：计算节点不再持有 KV cache，cache 放到独立的分层内存集群，用"advisory request"（从用户交互/请求结构预测出的预取提示）把 KV 搬运移出关键路径，实现请求级负载均衡。

## 要点
- advisory request 真阳性率 100%（真实请求必然先有提示），但存在假阳性 → 需要容错机制
- 两个核心手法：按网络结构与请求优先级分配内存的 priority-based KV 管理；多节点协同的 cooperative memory scheduling
- 每节点一个 node manager，对外只暴露四类接口：advisory、推理请求、跨节点 KV fetch、invalidation
- 与 [[MLSys2026论文精选]] 的 HiSparse 分层内存、[[HBF-NAND-AI推理内存2026]] 的容量层是同一命题：KV 已放不进 GPU，搬到哪里 + 谁来预测搬运
