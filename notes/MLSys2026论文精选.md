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

## LMetric：乘法打分简化 LLM 请求调度（OSDI'26，2026-08-13 归档）
整理自 [[inbox/2026-08-10-MLSys顶会-OSDI26推理论文]]
- 上交 IPADS × 阿里：两个指标简单相乘（KV 感知的 prefill token 数 × 负载感知的实例 batch size）做跨实例请求路由，免超参调优
- 相比 vLLM-v1 与生产调度器，TTFT 最高降 92%/39%，TPOT 降 24%/51%，已在阿里生产环境上线验证
- 关键洞察：乘法打分等价线性组合、超参在比较中抵消；论文给出乘法失效的数学条件（arXiv 2603.15202）
- 关联 [[推理引擎格局-vLLM-SGLang]]

## MLSys 2026 落幕：Agentic 系统与 Kernel 工程化主线（2026-08-13 归档）
整理自 [[inbox/2026-08-10-MLSys顶会-MLSys2026大会趋势]]
- 主线：Agentic AI 的系统支撑 + Kernel 工程化；缓存管理/投机解码/RAG/Agent 记忆论文密集（AgenticCache、Hippocampus）
- 推理优化：MAC-Attention、BLASST（注意力稀疏化）、TokenWeave（计算-通信重叠）
- FlashAttention-4 迁至 CuTe-DSL/Python 便于扩展（详见上方 [[#FlashAttention-4: 面向 Blackwell 非对称硬件扩展]]）
- 竞赛转向 kernel 生成：AWS Trainium MoE 挑战赛、NVIDIA FlashInfer AI Kernel 生成赛
- 关联 [[AI-Agent-2026格局]]、[[推理引擎格局-vLLM-SGLang]]

## Strata：长上下文 LLM 分层缓存框架（OSDI'26，2026-08-15 归档）
整理自 [[inbox/2026-08-12-MLSys顶会-OSDI26-Strata分层缓存]]
- Stanford/NVIDIA 联合提出，面向长上下文 LLM 服务的分层上下文缓存
- 诊断三痛点：KV 碎片化小传输浪费带宽、缓存加载阻塞 prefill、调度器忽视加载延迟致「延迟命中」
- 方案：GPU 辅助 I/O（解耦 GPU 与主机布局、大块高效传输）+ 缓存感知调度（平衡 batch 隐藏加载延迟）
- 已集成 SGLang 上线生产，吞吐最高达 vLLM-LMCache 5×、TensorRT-LLM 3.75×
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]

## OpGuard：位对齐调试生产级 LLM 训练（OSDI'26，2026-08-18 归档）
整理自 [[inbox/2026-08-14-MLSys顶会-OpGuard位对齐调试]]
- UMich OrderLab × ByteDance Seed 提出"位对齐"（bitwise alignment）作 LLM 训练正确性 oracle 与调试原语：跨异构训练栈找语义稳定算子边界并打轻量指纹，定位两轮执行首个位不一致的 tensor 作调试枢轴
- 已部署字节预训练/后训练，诊断 20+ 生产问题（kernel 竞态、静默数据损坏），调试时间从数天缩至数分钟
- 无需"黄金基线"即可比较，通过控制良性非确定性让首个不匹配点成为强错误证据

## Kairox：在线神经元均衡的 GPU-CPU 混合 LLM 推理（OSDI'26，2026-08-18 归档）
整理自 [[inbox/2026-08-14-MLSys顶会-Kairox在线神经元均衡]]
- SYSU × 鹏城实验室 × EPFL：自适应 GPU-CPU 混合推理，用在线神经元均衡按激活模式动态在 GPU/CPU 间重分布 FFN 神经元，缓解静态"热/冷"划分无法适配运行时激活变化的问题
- Live Pipeline 预取神经元（预测下一层激活模式）+ Temporal Activation Momentum 缓存策略优先保留持续活跃神经元
- 相比静态划分，动态重分布提升混合推理吞吐；关联 [[推理引擎格局-vLLM-SGLang]]

## OSDI'26 最佳论文：DynaRL + ValScope（2026-08-19 归档）
整理自 [[inbox/2026-08-16-MLSys顶会-OSDI26最佳论文]]
- OSDI'26（西雅图 7/13-15）最佳论文双揭晓：DynaRL（大规模 RL 训练弹性调度）与 ValScope（DBMS 逻辑 Bug 检测）同获 Best Paper
- DynaRL：首个面向大规模 RL 训练的弹性调度系统，用动态超图建模整条 RL pipeline，跨组件实时迁移算力/显存/通信，数学推理与 Agentic RL 负载吞吐最高 1.98×（PKU/Infinigence-AI/中科院等）
- ValScope：价值语义感知蜕变测试，在 6 款数据库（MySQL/OceanBase/TiDB 等）发现 67 个未知逻辑 Bug、57 个确认、48 个可追溯至 2020 年前（最长潜伏 20 年）；厦大首篇 OSDI
- 关联 [[推理引擎格局-vLLM-SGLang]]

## Modular：MLSys 2026 三大趋势与最佳论文（2026-08-21 归档）
整理自 [[inbox/2026-08-18-MLSys顶会-MLSys2026趋势与最佳论文]]
- 三大趋势：LLM 推理异构硬件、KV cache 精细管理、「系统智能」
- **SuperInfer**：现有 offload 框架仅用 GH200 NVLink-C2C 900GB/s 带宽的 <5%，瓶颈在软件栈而非硬件
- 最佳论文聚焦 RAG 推理（**TeleRAG**，lookahead retrieval）与 MoE 训练内存墙（**MoEBlaze**）
- Lidong Zhou 提出「System Intelligence」：AI agent 已成底层代码主要作者（证明通过率 2%→91.3%）
- 关联 [[AI-Agent-2026格局]]、[[#MLSys 2026 总结与三大趋势]]

## SOSP 2026 录用论文：AI/LLM 系统成主角（2026-08-24 归档）
整理自 [[inbox/2026-08-20-MLSys顶会-SOSP2026录用论文]]
- 第 32 届 SOSP 2026（9/29-30 布拉格）接收率 15.9%（62/390），AI/LLM 系统方向占大量篇幅
- TensorDex (UVA/Harvard)：张量中心 AI 模型存储系统；TensorHub：LLM RL 训练弹性权重传输
- CrystalLLM (阿里/上交/哈佛/浙大)：用少量 GPU 忠实模拟大规模 LLM 训练
- Linux AGX (UCR)：面向物理 AI 与机器人系统的 Linux GPU 公平调度扩展
- 推理服务：AWS SystemX 全球规模推理、北大/京东 Multi-LLM Serving、LMTracer 细粒度性能剖析

## OSDI'26 最佳论文补充：LLM 预训练数据管线（USTC × 字节 Seed，2026-08-26 归档）
整理自 [[inbox/2026-08-22-MLSys顶会-OSDI2026最佳论文]]
- 中科大李诚团队联合字节 Seed 凭「Teaching The Old Dog New Tricks: Building Efficient Data Pipelines for Large-Scale LLM Pre-training」获 Jay Lepreau 最佳论文奖（Operational Systems 赛道）
- 基于 3 万余真实训练任务数据，首次系统揭示 LLM 预训练数据管线三大瓶颈：伴生评测引发跨数据中心流量、启动阶段初始化 I/O 风暴、多模态数据变换 CPU 密集
- 三项优化：预测性 checkpoint 复制、主动热文件复制、存储侧数据变换卸载
- 同期最佳论文另有 DynaRL、ValScope（见 [[#OSDI'26 最佳论文：DynaRL + ValScope（2026-08-19 归档）]]）、布朗大学 Semisolates；OpGuard 获最佳论文提名
- 信号：数据管线成为继算力/通信之后的大模型训练新优化焦点

## SOSP 2026 推理新论文：Model2Kernel 与 LLM-42（2026-08-28 归档）
整理自 [[inbox/2026-08-24-MLSys顶会-SOSP2026推理确定性与内核安全]]
- **Model2Kernel**（PSU×北大×中科院）：首个实用 CUDA 内核内存安全自动验证系统，模型感知动态分析 + CUDA 符号执行，在 vLLM/HuggingFace 内核发现 353 个未知 bug、仅 9 个误报
- **LLM-42**（微软研究院）：用「解码-验证-回滚」的 verified speculation 实现确定性 LLM 推理，基于 SGLang，仅 is_deterministic 请求触发验证，无需重写 GPU 内核
- 确定性机制：固定 shape 重放验证 + 回滚不一致 token；关联 [[推理引擎格局-vLLM-SGLang]]、[[#SOSP 2026 录用论文：AI/LLM 系统成主角（2026-08-24 归档）]]

## SOSP 2026 录用全集公开 + MLSys 2027 CFP（2026-08-30 归档）
整理自 [[inbox/2026-08-26-MLSys顶会-SOSP2026录用全集与MLSys2027CFP]]
- SOSP 2026（9/29–10/2 布拉格）完整录用名单公开，camera-ready 8/28 截止；新见论文：ProbeFS（上交 DNA 文件系统）、QProf（Google 仓库级成本剖析）、RPCShield（微服务级联故障）、Welder（控制面活性验证）、XTRA（CXL 缓存一致性 + 事务并发）、M2K（CUDA kernel 验证）
- AI/LLM 系统方向延续主力（CrystalLLM、TensorDex、Linux AGX、LMTracer 等，见 [[#SOSP 2026 录用论文：AI/LLM 系统成主角（2026-08-24 归档）]]）
- MLSys 2027 CFP 已上线：投稿 10/31 截止、2027 年 5 月举办；工业 Track 继续，Artifact Evaluation 对标 ASPLOS/CGO/PLDI

## SOSP 2026 录用全集：AI 系统与编译器新论文（2026-09-01 归档）
整理自 [[inbox/2026-08-28-MLSys顶会-SOSP2026完整录用名单-AI系统论文]]
- 第 32 届 SOSP（9/29–10/2 布拉格）62 篇录用论文完整名单公开，camera-ready 8/28 截止
- Guiding Agentic GPU Kernel Optimization（HKUST×Stanford）：数据流不变量引导 LLM 做 GPU 内核优化，编译器与 Agent 深度结合
- Wavel + MeshRT（爱丁堡×微软）：面向晶圆级加速器的编译系统与运行时，编译期治理低延迟高吞吐推理
- Taming Dynamism on GPUs（上交）：跨 SM 内核融合 + JIT 归约；Democratizing MoE（USC×DeepMind）：免屏障专家并行解码
- ACDC（上交×阿里）：离线 LLM 推理剖析优化；TuxBot（哥大×IBM）：LLM 语义感知 OS 在线调优
- 关联 [[#SOSP 2026 录用全集公开 + MLSys 2027 CFP（2026-08-30 归档）]]、[[AI编译器2026格局]]

## "KV Cache 互联网"：重构 LLM 推理基础设施边界（2026-09-03 归档）
整理自 [[inbox/2026-08-30-MLSys顶会-KV-Cache互联网重构推理基础设施]]
- arXiv 2608.01526 愿景：KV Cache 复用已把 LLM 推理从「算力-存储权衡」变成「互联网级内容分发」问题，主张把网络与存储当作推理的一等资源
- 为缓存状态建立分布式查找/路由/迁移的控制面：KV 可重算或复用，最优位置取决于存储-网络-重算权衡而非云/管理边界
- 控制面需分布式 lookup + 带宽与截止时间估算，决策移动/复制/部分取/重算
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]

## ADAngel：任意精度量化 LLM 的自适应计算映射（OSDI'26，2026-09-01 归档）
整理自 [[inbox/2026-09-01-MLSys顶会-ADAngel任意精度量化GEMM]]
- 上交 ADAngel 面向任意精度量化 (APQ，如 W4A8) 的 mpGEMM，现有静态、负载不感知的 kernel 只能加速相似 shape/位宽任务
- 提出 DPR（分解-部分积-重构）计算模型，系统生成多样化 mpGEMM 算法族，按工作负载自适应选 kernel
- 关联 [[推理引擎格局-vLLM-SGLang]]

## StriaTrace：在线 LLM 推理的低开销追踪诊断（OSDI'26，2026-09-01 归档）
整理自 [[inbox/2026-09-01-MLSys顶会-StriaTrace在线推理追踪诊断]]
- 上交 × 阿里，面向受细粒度 SLO 约束的在线推理服务，解决现有工具追踪开销大、难捕获偶发异常的问题
- 三原则（只追关键同步点/关键路径/异常时详追）+ 动态回归 roofline 模型 + 相关性诊断定位根因
- 追踪开销较替代方案降 97.8%，已在阿里生产落地，诊断 19 类根因数百次异常
- 关联 [[推理引擎格局-vLLM-SGLang]]

## ASPLOS 2026：PIM 存内计算 + KV Cache 卸载成 LLM 推理新焦点（2026-09-06 归档）
整理自 [[inbox/2026-09-03-MLSys顶会-ASPLOS2026-PIM与KV-Cache卸载]]
- REPA（SJTU）：可重构 PIM 联合加速 KV Cache 卸载与处理，co-design 数据搬移与计算
- STARC（RPI/UMass/IBM）：token 选择性访问 + 重映射/聚类，正交于量化与淘汰式压缩
- ZipServ（港科广/哈工深/港科）：硬件感知无损压缩降内存占用且不损精度
- 趋势：PIM 与 KV Cache 卸载同频 HBF/CXL 内存外移；关联 [[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]

## MLSys 2026 推理服务新论文：调度与能效优化（2026-09-09 归档）
整理自 [[inbox/2026-09-05-MLSys顶会-MLSys2026-serving新论文]]
- 主线从「更快」转向「更省、更稳」：能耗感知调度、运行时量化层交换、prefill 长度感知调度成为新方向
- MorphServe：运行时量化「层交换」+ KV cache 弹性缩放，负载感知降本
- BEAM：SLO 约束下资源-功耗联合优化，能效优先的 LLM 推理
- PLA-Serve：prefill 长度感知调度；BOute：异构 LLM/GPU 多目标贝叶斯寻优
- 关联 [[推理引擎格局-vLLM-SGLang]]

## SOSP 2026 完整录用：能耗感知调度与 Agent 原生文件系统（2026-09-09 归档）
整理自 [[inbox/2026-09-09-MLSys顶会-SOSP2026完整录用AI系统新论文]]
- 62 篇录用名单公开（9/29–10/2 布拉格），新增方向：能源感知调度、加密推理、Agent 原生文件系统、CXL 内存
- Beyond Utilization（UT Austin）：推理 GPU 共享从「利用率」转向能耗感知调度；Cerium（CMU×NVIDIA）：TB 级加密推理多 GPU 框架；DiFlow（阿里×港科）：文生图扩散微服务编排
- Don't Let AI Agents YOLO Your Files（Wisconsin×MSR）：Agent 原生文件系统；Borges（UT Austin×Anthropic）：CXL 内存/SSD 混合共享日志；AgileLog（UIUC）：数据流 Agent 可分叉共享日志
- Morphable Kernels（清华）：GPU 多任务形态可塑内核；关联 [[#SOSP 2026 录用全集：AI 系统与编译器新论文（2026-09-01 归档）]]

## OSDI'26｜Hetu v2：分层异构 SPMD 标注统一异构训练（2026-09-14 归档）
整理自 [[inbox/2026-09-11-MLSys顶会-OSDI26-Hetu-v2-HSPMD]]
- 北大/上交/普渡提出 HSPMD：把 SPMD 的声明式标注下沉扩展为非对称分片 + 分层通信原语组合，用同一套单设备视角的编程模型表达异构并行
- 路线选择：不堆调度器层，而把扩展性推到 primitive 层；空间异构用渐进式图特化给不同设备生成各自执行逻辑，时间异构靠训练中动态切换分片/通信策略
- 对 GP+CC 异构 SoC 直接可参考：非对称分片 = 不同核心承担不同 shard 尺寸
- 关联 [[推理引擎格局-vLLM-SGLang]]

## OSDI'26｜Syncopate：以「通信块」为中心的细粒度计算通信重叠（2026-09-14 归档）
整理自 [[inbox/2026-09-11-MLSys顶会-OSDI26-Syncopate-chunk重叠]]
- UCSD/Meta/OpenAI/GMU 指出分布式编译器现状只在 stream 级别整 kernel 重叠计算与通信，导致额外 kernel 启动、kernel 边界全设备同步、慢 tile 拖尾留下大量空隙
- 提出 communication chunk 抽象，把通信粒度与 kernel 结构、后端机制解耦，做单 kernel 内的细粒度重叠：输入本地 Triton kernel + chunk 调度方案，源到源编译重排 tile 执行顺序以跟随通信进度
- chunk 计划来源三选一：从现有分布式编译器移植、用户手写、复用模板；收益是免去额外 kernel 启动与边界同步
- 趋势：分布式编译从「流级重叠」走向「tile/chunk 级重叠」，与 Triton-distributed、Event Tensor 同向；关联 [[AI编译器2026格局]]
- 关联 [[推理引擎格局-vLLM-SGLang]]

## MLSys 2026 概况：Competition Track 与 2027 投稿窗口（2026-09-13 归档）
整理自 [[inbox/2026-09-13-MLSys顶会-MLSys2026论文竞赛与2027投稿窗口]]
- 第九届 MLSys（5/18–22，Bellevue WA）：General Chair Luis Ceze（UW/NVIDIA），Program Chair Zhihao Jia（CMU）；新增独立 Industry Track CFP，并首次设 Competition Track
- 竞赛赛道暴露硬件厂商真实诉求：AWS Trainium2/3 MoE Kernel Challenge、Google Graph Scheduling Competition、NVIDIA FlashInfer AI Kernel Generation Contest（AI 生成 kernel 成正式比赛项目）
- 产业论文示例：SAKURA ONE 用 800GbE + SONiC 构建开放以太网 AI HPC 集群，并分析单租户 LLM 开发环境下的负载动态
- 2027 周期：投稿开放 10/10/26、截止 10/30/26、通知 2/28/27；2026 session 录像因硬盘运输丢失无法恢复
- 关联 [[#MLSys 2027 CFP 与 OSDI'26 工业 Track（2026-09-11 归档）]]

## OSDI'26 训练可靠性专场：SDC 表征与角色级容错（2026-09-17 归档）
整理自 [[inbox/2026-09-17-MLSys顶会-OSDI26-SDC静默损坏与训练可靠性]]
- 场次「Training Reliability and Silent Errors」四篇串成表征→检测→定位→恢复链路；核心结论：合成微基准漏掉 60%+ 缺陷设备，ECC/温度保护抓不到逻辑级 bit flip
- **SDCs in the Wild**（SJTU+字节 Seed，Operational Systems）：表征生产集群捕获的 23 块 SDC 缺陷 GPU——不只出现在新卡（多因老化）、高度数据依赖且单元特定（通过通用压测的卡在特定训练输入下失败）；SDCHunter 改用「复现触发失败的确切负载+输入数据」诊断，字节部署缓解 40 起事件
- **AEGIS**（清华+字节）：cSensor–cVerifier 两阶段解耦（轻量感知 + 确定性验证），3.5×10⁷ GPU 小时部署发现 18 起真实 SDC/13 块坏卡，开销 0.86%
- **RobustRL**（浙大）：RL 后训练引入角色级故障隔离（trainer/rollout/管理角色为独立子任务），只恢复失败角色而非整任务重启；Detect-Restart-Reconnect + UCX 动态点对点通信替代静态集合通信；256 GPU/10% 故障注入下 ETTR >80%（ByteRobust 60%），端到端快 8.4–17.4%
- 趋势：可靠性从「整任务重启」走向「角色/单元级隔离 + 用真实负载复现定位」；关联 [[AI芯片推理硬件2026全景]]

## OSDI'26 RL 后训练系统集群：分离架构带来的新系统层（2026-09-17 归档）
整理自 [[inbox/2026-09-17-MLSys顶会-OSDI26-RL后训练系统集群]]
- 6 篇扎堆（Weave/RollArt/RLinf/DynaRL/Seer/RobustRL），共同前提是 rollout（带宽密集）与训练（算力密集）物理分离到各自集群；分离后的依赖气泡、硬件错配、长尾延迟成为与算法正交的独立议题
- **Weave**（HKUST+阿里）：co-execution group 把集群切为隔离 locality domain，组间保守随机规划做放置、组内可证明最优轮转调度，residency 约束让巨型模型状态驻留主机内存实现 warm-start 切换；328 H20+328 H800 成本效率 1.84×/1.38×，100% SLO
- **RollArt**（HKUST+通义）：阶段-硬件匹配（prefill→算力优化 GPU、decode→带宽优化 GPU、环境→CPU 集群），轨迹级解耦 rollout，stateless reward 卸载 serverless，staleness-bounded 异步权重同步；训练时间降 1.31–2.05×，阿里 3000+ GPU 训百亿级 MoE 供 Qoder
- **Seer**（月之暗面+清华）：同 prompt 请求的输出长度/模式高度相似 → divided rollout + context-aware 调度 + 自适应分组投机解码；rollout 吞吐 2.04×，长尾延迟降 72–94%
- **RLinf**（清华/无问芯穹）：M2Flow 把 RL 工作流在时空两维自动分解重组，端到端吞吐 1.07–2.43×
- 关联 [[推理引擎格局-vLLM-SGLang]]

## OSDI'26 录用 136/681（20.0%）与调度侧反直觉结果（2026-09-13 归档）
整理自 [[inbox/2026-09-13-MLSys顶会-OSDI26-20%录用率-LLM系统方向]]
- 录用率 20.0%；规则变化：新增 Operational Systems 轨道（生产系统运维论文有独立席位）、取消 author response、用 conditional accept 取代 revise-and-resubmit
- 长上下文 KV 管理：Strata（SGLang 内落地）、ECHO（原生稀疏注意力 KV 卸载 + 无损预取，2.1×）、No Buffer No Bottleneck（零拷贝 KV 卸载）三条路线并列
- 调度反直觉：Simple Is Better（「乘法可能就够了」的请求调度）；Prism 用 GPU 显存 ballooning 做多 LLM 混合服务；成本导向的资源高效服务成为独立 session
- 内存分层：OBASE 指出「热对象碎片化」——活跃页中最多 97% 字节是冷却不可回收的；用编译器-运行时的对象感知地址空间重排（指针插桩 + 无锁对象迁移）让未改动的 kswapd/TMO/TPP/Memtis 生效，页利用率 2–4×、内存占用最多降 70%、开销仅 2–5%
- GPU 编译：Optimal Software Pipelining and Warp Specialization for Tensor Core GPUs（Stanford + NVIDIA）把 SWP 与 warp specialization 的组合求解自动化
- 关联 [[#Strata：长上下文 LLM 分层缓存框架（OSDI'26，2026-08-15 归档）]]、[[推理引擎格局-vLLM-SGLang]]

## MLSys 2026 三大趋势（Modular）与 OSDI'26 流水线并行细节（2026-09-15 归档）
整理自 [[inbox/2026-09-15-MLSys顶会-MLSys2026三大趋势]]、[[inbox/2026-09-15-MLSys顶会-OSDI26重访流水线并行]]
- Cache as a system 的具体形态（Modular 复盘 MLSys 2026，5/18–22 Bellevue）：SGLang *HiSparse* 把稀疏注意力与分层内存配对，GPU 只留热点 KV、冷数据下沉主机内存，GLM-5.1-FP8 长上下文吞吐最高 5×；Kitty 用 2-bit KV 量化 + 动态通道级精度提升，同显存下 batch 大 8×；Brian Zhang《The Five Eras of KVCache》把演进分为引擎内优化 → PagedAttention → 前缀缓存 → offload → 统一/分布式/可组合
- Agent 写系统代码的边界：Mark Saroufim 演示 Agent 写 kernel 能冲榜，但也会「作弊」benchmark；Nanvix Rust 微内核用 AI 生成规格与证明，150 任务证明生成通过率 2%（GPT-4o）→ 91.3%（微调 LLaMA-3.1 8B + self-debug）
- 流水线并行再审视（Korea Univ.，Soonjae Hwang / Jeongseob Ahn）：在线服务把阶段间计算不平衡显著放大——prefill 长短混批导致 P-P 失衡，全 decode microbatch 导致 D-D 失衡（一个 microbatch 扛很多请求、另一个很少），纯静态 chunk 划分与既有技术都消不掉气泡
- 三个手段：greedy 与 predictive 两种动态 chunk size 调整消除 prefill 气泡，delay scheduling 跨阶段动态再平衡 decode 负载 → 论证 PP 在吞吐上仍有超越 TP 的空间，对 MoE/长上下文服务是可复刻的调度思路
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[AI编译器2026格局]]
