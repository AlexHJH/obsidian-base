# MLSys 2026 论文精选

- 整理自: [[inbox/2026-06-14-ExecuTorch-MLSys最佳工业论文]], [[inbox/2026-06-14-LEANN-MLSys最佳论文]], [[inbox/2026-06-17-OSDI26-ML系统论文]]（2026-06-20 OSDI'26 新一批论文已归档合并）
 整理自: [[inbox/2026-06-14-ExecuTorch-MLSys最佳工业论文]], [[inbox/2026-06-14-LEANN-MLSys最佳论文]], [[inbox/2026-06-17-OSDI26-ML系统论文]]（2026-06-20 OSDI'26 新一批论文已归档合并），[[inbox/2026-06-23-MLSys2026-LLM推理系统论文速览]], [[inbox/2026-06-23-OSDI26-OpenTela异构HPC集群LLM服务]]
- 标签: #MLSys #端侧推理 #PyTorch #向量检索 #RAG #OSDI

## ExecuTorch: 最佳工业论文
PyTorch 官方端侧推理框架，统一覆盖手机/MCU/嵌入式设备。通过 XNNPACK delegate + Arm SME2 实现 3.9× 端到端加速，支持 INT8/FP16 量化和算子级剖析，已成为 PyTorch 端侧部署标准。

## LEANN: 最佳研究论文
低存储向量索引，通过即时重计算 embedding + 压缩邻近图，将索引体积缩小 50×（仅占原始数据 ~5%），top-3 召回仍达 90%，延迟与标准索引可比。使端侧 RAG 在个人设备上可行，GitHub 11K stars。作者来自 UC Berkeley Sky Lab（Ion Stoica、Matei Zaharia、Joseph Gonzalez 等）。核心价值：让私有数据 RAG 摆脱云端依赖。

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
整理自 [[inbox/2026-07-06-系统顶会-OSDI26议程与厦大首篇]]
- **厦大首篇 OSDI**：数据库管理系统正确性研究，标志国内系统研究向顶会突破
- **ECHO**（SJTU/华为）：KV Cache 无损预取卸载，针对原生稀疏注意力 LLM 推理优化
- **Xkernel**：用编译器技术自动化 OS 内核参数调优，重新思考内核性能可调优性
- **Sereno**（SJTU/华为）：治移动端 LLM 推理的内存带宽竞争，端侧瓶颈转向带宽而非算力
- **FlowANN**：拆解图依赖做十亿级 GPU 向量搜索，RAG 检索基础设施级优化
- **Revisiting Pipeline Parallelism for LLM Serving**（高丽大学）：重审 LLM 服务的流水线并行调度
  - 整理自 [[inbox/2026-07-10-系统顶会-OSDI2026-Pipeline-Parallelism]]（2026-07-14 归档）
  - 挑战 TP 统治地位，通过 Greedy/Predictive chunk size 动态调整 + Delay Scheduling 重平衡 decode 负载，证明合理调度下 PP 可超越 TP
- **Oxbow**（Tianyin Xu 团队）：多组件文件系统的协同架构，OSDI'26 Operational Systems 赛道
- OSDI'26（7月13-15日西雅图）取消 author response、新增 Operational Systems 赛道（OpenTela 入选）

## Meta Llama 推理部署配置优化 (MLSys 2026 Oral)
整理自 [[inbox/2026-07-07-推理-MLSys2026-Meta-Llama部署优化]]
- Meta 系统性分析 Llama 推理部署的数百万种配置组合，覆盖 H100/H200/MI300X GPU
- 并行策略对比：张量/流水线/专家/上下文并行；continuous batching vs prefill-decode 分离
- 核心发现：并行策略有阶段特异性（prefill vs decode 最优配置不同）；MoE 架构引入系统级新权衡
- scale-up（高带宽互联）vs scale-out（标准网络）：超大模型场景下 scale-up 未必经济
- vLLM 与 DeepLearning.AI 联合推出「Fast & Efficient LLM Inference」课程（2026-06）
- 关联 [[推理引擎格局-vLLM-SGLang]]

## OSDI 2026 开幕（2026-07-16 归档）
整理自 [[inbox/2026-07-13-系统顶会-OSDI2026开幕]]、[[inbox/2026-07-23-MLSys-OSDI2026回顾]]
- 7/13-15 西雅图，第20届 OSDI，接收率目标≥20%（历史~16.6%），取消 author response
- **ValScope**（厦大）：数据库逻辑 Bug 检测，国内系统研究向顶会突破
- **μShell** 获 Distinguished Artifact Award：基于微内核的 FPGA Shell 架构
- 新增 Operational Systems Track，鼓励工业界运维系统实践投稿
- 与 MLSys 社区交叉增多，ML 系统方向占比持续上升

## Guard：大规模训练 Straggler 检测
整理自 [[inbox/2026-07-17-MLSys-大规模训练straggler检测Guard]]
- 面向万卡级大模型训练的 straggler 检测系统，结合在线性能监控与离线节点筛查
- 双机制覆盖：在线轻量监控（急性故障）+ 离线节点 sweep（慢性性能退化）
- 效果：MFU 提升最高 1.7×，训练 step 方差从 20% 降至 1%，MTTF 显著增加
- 能检测传统诊断工具无法捕获的 fail-slow 行为

## FlashAttention-4: 面向 Blackwell 非对称硬件扩展（2026-07-25 归档）
整理自 [[inbox/2026-07-22-MLSys-FlashAttention4-Blackwell]]
- Together AI / Dao-AILab 在 MLSys 2026 发布，针对 Blackwell GPU 非对称硬件扩展（Tensor Core 翻倍但共享内存/指数单元未同步升级）
- B200 BF16 达 1613 TFLOPs/s（71% 利用率），比 cuDNN 9.13 快 1.3×，比 Triton 快 2.7×
- 全异步 MMA + 更大 tile，软件模拟指数 + 条件 softmax 缩放，削减非 matmul 操作占比
- 揭示 Blackwell 新瓶颈：shared memory 带宽和指数单元成新制约

## 信号
- 端侧 AI 从「能不能跑」进入「跑得好不好」阶段
- 存储效率成为端侧推理新瓶颈，动态重算思路值得关注
- 向量检索 + 端侧推理 = 隐私保护的本地 RAG 成为可能
- OSDI'26 新增 Operational Systems 赛道

## NSDI 2026：LLM 训练与服务系统（2026-06-27 归档）
整理自 [[inbox/2026-06-27-系统顶会-NSDI2026-LLM训练与服务系统]]
- **SYMI**：MoE 训练将 expert 参数与 optimizer state 分离放置，降显存压力
- **Di-PS**：算法-系统协同设计，跨集群异步异构训练
- **FastServe**：迭代级抢占式推理调度，提升 GPU 利用率
- **EROICA**：集群级在线性能诊断
- 趋势：系统-算法协同（co-design）成为训练扩容主流路径

## MLSys 2026 论文速览补充（2026-07-30 归档）
整理自 [[inbox/2026-07-26-MLSys-OSDI2026回顾]]
- **SHIP**：SRAM-based 推理管线；**PRISM**：投机解码 draft model
- **FlashAgents**：多 agent 流式 prefill 重叠；**SuperInfer**：superchip 调度
- **OpenHands SDK** 亮相；**Hippocampus**（agent 记忆模块）；**AgenticCache**（具身 agent 异步规划）
- OSDI'26 新增 Operational Systems track，接收率目标 ≥20%；μShell 获 Distinguished Artifact Award（微内核 FPGA Shell 架构）
- LLM serving 从 3 个 session 扩至 6 个，成绝对主角

## MLSys 2026 总结与三大趋势（2026-07-16 归档）
整理自 [[inbox/2026-07-08-系统顶会-MLSys2026总结]]、[[inbox/2026-07-13-MLSys2026三大趋势]]
- **Agent 写系统代码**：新手用 AI Agent 写出接近 hackathon 顶级的 kernel；Lidong Zhou 呼吁系统社区为 Agent 作为代码主要作者做准备
- **LLM Serving 爆发**：6 个 session（翻倍），FlashAgents、Cascade (MoE 投机解码)、HiSpec、LMCache
- **稀疏计算成标配**：非结构化稀疏、动态稀疏注意力用于超长上下文训练和推理
- 关联 [[AI-Agent-2026格局]]、[[推理引擎格局-vLLM-SGLang]]

## KV Cache 外移专题与 Modular 三大趋势（2026-08-09 归档）
整理自 [[inbox/2026-07-31-MLSys-STAR-KV-ICML2026]]、[[inbox/2026-07-31-MLSys-CXL-KV-Cache]]、[[inbox/2026-07-29-MLSys-2026三大趋势]]
- **STAR-KV**（Dnotitia，ICML 2026 Spotlight）：结构化稀疏 + 自适应缓存淘汰实现最高 20× KV Cache 压缩，长上下文质量近无损
- **CXL 内存池化**（TrendForce）：KV Cache 随上下文线性增长、单机 HBM 成瓶颈，CXL 跨节点共享内存池成关键方案，多家厂商已出原型
- **Modular 三趋势**：Agent 写系统代码成现实（KernelPro 等已达 hackathon 顶级）；异构硬件（GPU/NPU/MPU）成推理标配；KV Cache 向 DRAM/SSD/分布式多层外移，Netpreme 推专用 Memory Processing Unit（宣称 XPU 内存扩展 100×）
- MLSys 2026 竞赛 Track：Trainium2/3 MoE Kernel、Google Graph Scheduling、NVIDIA FlashInfer AI Kernel Generation
- 关联 [[推理引擎格局-vLLM-SGLang#KV Cache 瓶颈与 TurboQuant 4-bit 量化]]、[[AI编译器2026格局#Triton DSL 性能评估 & KernelPro LLM 优化]]
