# MLSys 2026 论文精选

- 整理自: [[inbox/2026-06-14-ExecuTorch-MLSys最佳工业论文]], [[inbox/2026-06-14-LEANN-MLSys最佳论文]], [[inbox/2026-06-17-OSDI26-ML系统论文]]（2026-06-20 OSDI'26 新一批论文已归档合并）
 整理自: [[inbox/2026-06-14-ExecuTorch-MLSys最佳工业论文]], [[inbox/2026-06-14-LEANN-MLSys最佳论文]], [[inbox/2026-06-17-OSDI26-ML系统论文]]（2026-06-20 OSDI'26 新一批论文已归档合并），[[inbox/2026-06-23-MLSys2026-LLM推理系统论文速览]], [[inbox/2026-06-23-OSDI26-OpenTela异构HPC集群LLM服务]]
- 标签: #MLSys #端侧推理 #PyTorch #向量检索 #RAG #OSDI

## ExecuTorch: 最佳工业论文
PyTorch 官方端侧推理框架，统一覆盖手机/MCU/嵌入式设备。通过 XNNPACK delegate + Arm SME2 实现 3.9× 端到端加速，支持 INT8/FP16 量化和算子级剖析，已成为 PyTorch 端侧部署标准。

## LEANN: 最佳研究论文
低存储向量索引，通过即时重计算 embedding + 压缩邻近图，将索引体积缩小 50×（仅占原始数据 ~5%），保持 SOTA 精度。使端侧 RAG 在个人设备上可行，GitHub 11K stars。

## OpenTela: 去中心化 HPC 集群统一 LLM 推理 (OSDI'26)
整理自 [[inbox/2026-06-16-OpenTela-OSDI26]]
- ETH Zurich 提出 P2P 去中心化架构，将异构 HPC 集群统一为 LLM 推理资源池
- 用 gossip 协议替代中心调度器 + CRDT 状态管理，网络分区下仍维持全局资源视图
- 支持异构 GPU 混合推理，已开源（Apache 2.0）并用于 SwissAI 生产环境
- 关联 [[AI芯片推理硬件2026全景]]
补充整理自 [[inbox/2026-06-23-OSDI26-OpenTela异构HPC集群LLM服务]]（2026-06-26 归档）
- 痛点定位：HPC(Slurm) 缺少 K8s 式控制面（服务发现/路由/负载均衡），算力跨域碎片化，节点不可外联
- 方案：CRDT gossip 容错服务发现 + 统一 API 适配多调度器/推理引擎 + 异构感知调度，全程用户态无特权
- USENIX 页标题 LLMFabric，PDF 名 OpenTela；作者跨 ETH/Cambridge/EPFL/MIT/HKUST
- 同组 BALBOA(OSDI 研究轨)：SmartNIC 上 RDMA 卸载引擎，增强数据中心网络服务

## MLSys 2026 LLM 推理系统：LMCache 与投机解码
整理自 [[inbox/2026-06-23-MLSys2026-LLM推理系统论文速览]]（2026-06-26 归档）
- **LMCache**：首个开源跨引擎 KV cache 层，将 vLLM/SGLang 的 KV cache 移出 GPU 显存并跨请求/引擎共享；CacheGen 压缩 + Cacheblend 非前缀共享，已在 30+ 公司生产落地(Google/AWS/NVIDIA/IBM)
- **Cascade**：效用驱动的 MoE 投机解码；**HiSpec**：分层投机解码；**HiServe**：混合 LLM 前缀缓存服务
- **Kascade**：长上下文稀疏注意力；另有「MoE 专家负载均衡的收益递减」分析
- 趋势：KV cache 外移复用 + MoE 专用调度成推理系统研究主线；关联 [[推理引擎格局-vLLM-SGLang]]

## OSDI'26 论文（ML 系统方向）
- **ECHO**（SJTU/华为）：KV Cache 无损预取卸载，针对原生稀疏注意力 LLM 推理优化
- **Xkernel**：用编译器技术自动化 OS 内核参数调优，重新思考内核性能可调优性
- **Sereno**（SJTU/华为）：治移动端 LLM 推理的内存带宽竞争，端侧瓶颈转向带宽而非算力
- **FlowANN**：拆解图依赖做十亿级 GPU 向量搜索，RAG 检索基础设施级优化
- **Revisiting Pipeline Parallelism for LLM Serving**（高丽大学）：重审 LLM 服务的流水线并行调度
- **Oxbow**（Tianyin Xu 团队）：多组件文件系统的协同架构，OSDI'26 Operational Systems 赛道
- OSDI'26（7月13-15日西雅图）取消 author response、新增 Operational Systems 赛道（OpenTela 入选）

## 信号
- 端侧 AI 从「能不能跑」进入「跑得好不好」阶段
- 存储效率成为端侧推理新瓶颈，动态重算思路值得关注
- 向量检索 + 端侧推理 = 隐私保护的本地 RAG 成为可能
- OSDI'26 新增 Operational Systems 赛道，[[inbox/2026-06-17-OSDI26-ML系统论文]] 同系列

## NSDI 2026：LLM 训练与服务系统（2026-06-27 归档）
整理自 [[inbox/2026-06-27-系统顶会-NSDI2026-LLM训练与服务系统]]
- **SYMI**：MoE 训练将 expert 参数与 optimizer state 分离放置，降显存压力
- **Di-PS**：算法-系统协同设计，跨集群异步异构训练
- **FastServe**：迭代级抢占式推理调度，提升 GPU 利用率
- **EROICA**：集群级在线性能诊断
- 趋势：系统-算法协同（co-design）成为训练扩容主流路径
