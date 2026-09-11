# 推理引擎格局：vLLM vs SGLang

- 整理自: [[inbox/2026-06-15-vLLM-SGLang推理引擎格局]], [[inbox/2026-06-17-vLLM-SGLang商业化-推理引擎]]
 整理自: [[inbox/2026-06-15-vLLM-SGLang推理引擎格局]], [[inbox/2026-06-17-vLLM-SGLang商业化-推理引擎]], [[inbox/2026-06-23-推理分离架构18月回顾-DisaggInference]]、[[inbox/2026-07-23-大模型推理-DeepSeek推理引擎开源与格局演变]]
- 标签: #推理引擎 #vLLM #SGLang #开源 #大模型部署

## 商业化（2026）
两引擎相继公司化，推理引擎成 AI 基础设施主战场：
- **Inferact (vLLM)**：a16z 领投 $150M 种子轮
- **RadixArk (SGLang)**：Accel 领投，$400M 估值
- DeepSeek 推理引擎基于 vLLM 早期分支魔改，将核心优化（PD 分离、EPLB 专家负载均衡、DualPipe 双流水线）贡献回馈社区（优先并入 vLLM/SGLang），实现 Day-0 SOTA 支持
- 选型：DeepSeek 模型 + 对话/RAG 场景 → SGLang（prefix-heavy 吞吐领先最高 6.4×）；多硬件 + 批处理 → vLLM（贡献者约 3× 于 SGLang）

## 社区起源
两者均源于 UC Berkeley Ion Stoica 实验室，但走了不同技术路线：
- **vLLM (2023.6)**：借 OS 分页思想实现 PagedAttention，吞吐提升最高 30×，社区 ~2000 PR 贡献者；核心贡献者来自 Red Hat
- **SGLang (2024.1)**：以 RadixAttention 扩展 KV cache 跨请求复用，社区规模为 vLLM 一半但增速快；来自 xAI/Oracle/LinkedIn
- 约 30% SGLang 贡献者亦给 vLLM 交过代码，技术趋于收敛

## 技术趋同
两者均已支持 Continuous Batching、Chunked Prefill、Speculative Decoding 等主流技术。2025 年初 vLLM V1 重构解决 CPU 调度开销瓶颈；DeepSeek V3/R1 发布推动两者二次爆发。
- 2025 年起从「数字军备竞赛」转向可复现的端到端评测
- 推理引擎是大模型产品化部署的基础设施核心

## 竞争者
- TensorRT-LLM（NVIDIA 硬件深度优化）
- llama.cpp（边缘推理）
- LMDeploy（双后端）

## Prefill-Decode 分离架构（2026）
Prefill（计算密集，打满 Tensor Core）与 Decode（访存密集，吃满 HBM 带宽）硬件需求相反，混跑同一 GPU 互相 stall。分离架构将两者拆到不同实例/集群，可获约 2 倍吞吐提升。2026 年 PD 分离已成为推理基建新范式。
- **vLLM 实现**：双实例（prefiller + decoder），用 connector 传输 KV cache；vLLM v1 支持 Chunked Prefill + Disaggregated Serving，兼容 OpenAI SDK
- **SGLang 实现**：PD 分离通过 mini_lb 路由调度，KV Cache 跨节点 RDMA 传输
- **Prefill-as-a-Service**：1 个 prefiller 服务多 decoder，跨集群进一步优化
- **核心挑战**：KV cache 跨节点传输开销；2026 年 NIXL（NVIDIA）+ UCX/GDS 成标准后端
- **落地**：Meta、Perplexity 已生产部署；腾讯云/阿里云 ACK 提供官方 PD 分离部署指南，解决 Agent 时代高并发推理瓶颈
- 整理自 [[inbox/2026-06-18-推理分离-Prefill-Decode-Disaggregation]], [[inbox/2026-07-21-大模型推理-PD分离架构成为2026推理基建新范式]]
补充整理自 [[inbox/2026-06-23-推理分离架构18月回顾-DisaggInference]]（2026-06-26 归档，UCSD Hao AI Lab 18 个月回顾）
- DistServe 提出 18 个月后，几乎所有生产级框架（NVIDIA Dynamo、vLLM、SGLang、LMCache、Mooncake、llm-d）均已采用分离架构
- **NIXL**（NVIDIA Inference Transfer Library）成 2026 KV cache 跨 GPU 传输标准，支持 RDMA 线速传输
- **NVIDIA Dynamo**：分离推理编排框架（路由 + KV 传输 + 自动扩缩容）
- **AMPD**（arXiv 2602.14516）：面向多轮对话的分离推理，解决增量 prefill 调度

## 关联
- Apple Silicon 本地推理见 [[MLX生态2026]]
- 算力扩张与先进封装见 [[AI芯片推理硬件2026全景]]

## KV Cache 瓶颈与 TurboQuant 4-bit 量化
整理自 [[inbox/2026-06-21-大模型推理优化全景-KVCache与TurboQuant]]、[[inbox/2026-07-27-大模型推理-KV-Cache优化2026]]
- 竞争核心从算力转向显存利用率/吞吐/单位 Token 成本/延迟四维博弈
- **KV Cache 显存瓶颈**：30B 模型 128k 上下文单条即占 32GB（FP16），超 RTX 4090 物理显存上限
- **TurboQuant**：99% 普通通道压至 4-bit、1% 离群通道保 FP16，显存降 75%、精度损 <1.2%
- **动态稀疏压缩（SITS 2026）**：按序列长度动态调整缓存粒度，实测降低 38% 显存碎片率
- **分层管理**：热 KV 留显存 → 温 KV 换出 CPU → 冷 KV 直接丢弃
- **系统化五件套**：INT8/INT4 量化 + KV Cache 分层 + 连续批处理 + FlashAttention + 推测解码
- **生产落地参考**：金融/科研 >70B 模型保精度，通用客服 32B 性价比最优
- 连续批处理：GPU 利用率 45% → 85%+，尾延迟降 60%
- 组合优化（量化 + vLLM + 稀疏化 + Serverless）：百万 Token 成本 $100 → $5，降本 95%

## 推理系统治理：多引擎控制平面（2026-06-25 归档）
整理自 [[inbox/2026-06-25-推理-推理系统治理与多引擎共存]]
- 推理层从「模型附属组件」升级为 AI Infra 核心层；推理成本已成 AI 商业化决定项
- 模型侧（MoE/多模态/Agent/超长上下文）+ 硬件侧（GPU/NPU/定制加速器）双线推高复杂度 → 多引擎并存成工程必然
- 控制平面（如 GPUStack）把推理从「项目级资产」升级为「平台级资产」
- 关联 [[AI-Agent-2026格局]]（Agent 长程任务推高推理复杂度）

## llm-d：Kubernetes 原生分布式推理控制平面（2026-08-31 归档）
整理自 [[inbox/2026-08-27-大模型推理-llm-d-K8s分布式推理]]
- Google/NVIDIA/IBM Research/CoreWeave 共创的开源 K8s 原生分布式 LLM 推理框架，作为 vLLM/SGLang 之上的集群级控制平面（基于 Envoy/vLLM/KServe），vLLM 首届大会 Day2 由 Red Hat 主讲，Red Hat AI Inference 开放第三方 K8s 早期访问
- 收益：输出吞吐 3×、TTFT 快 2×、tokens/s 最高 +70%；Google Vertex 上 TTFT/ITL 降 40%
- 支持 disaggregated serving、prompt 感知路由、专家并行，Helm 一键部署到 OpenShift

## 投机解码生产落地综述（2026-07-18 归档）
整理自 [[inbox/2026-07-18-推理-投机解码生产落地vLLM-SGLang]]
- vLLM 支持 EAGLE/MTP/draft model/ngram 多种投机方法，EAGLE/MTP 收益最高
- SGLang RadixAttention 在 Agent 长程任务中投机+缓存双重收益
- 低 QPS 内存瓶颈场景收益最大，高 QPS 算力瓶颈收益递减
- llama.cpp 也支持投机解码，本地部署可用小 draft 模型加速

## DSpark：置信度驱动的投机解码（2026-09-06 归档）
整理自 [[inbox/2026-09-06-大模型推理-SGLang-DSpark置信度投机解码]]
- SGLang 发布 DSpark：分块半自回归起草，按 draft 自身置信度动态决定验证窗口大小，取代固定 draft 长度
- DeepSeek-V4-Pro 于 TP8 B300（bs=1）达 383.7 tok/s、accept length ~5；用 `--speculative-algorithm DSPARK` 启用
- 同期推 SGLang-Diffusion 实时视频生成；关联 [[#投机解码生产落地综述]]

## EAGLE-3 自推测解码（2026-06-25 归档）
整理自 [[inbox/2026-06-25-推理-EAGLE3自推测解码]]
- EAGLE-3 用目标模型自身内部状态做 self-speculation，省去独立 draft 模型显存
- DeepSeek-R1 Multi-Token-Prediction 为同类「模型原生多 token 预测」思路
- TensorRT-LLM 推测解码最高 3.6× 吞吐；接受率（acceptance rate）决定收益

## Stream2LLM：流式预填降低 TTFT（2026-06-28 归档）
整理自 [[inbox/2026-06-28-推理-Stream2LLM流式预填降低TTFT]]
- MLSys 2026 论文，扩展 vLLM 支持并发下的流式输入，将上下文流式传输与预填重叠
- TTFT 较非流式基线降低 3.9–11×；低负载下各流式调度器性能趋同，高负载下收益最大
- 两阶段调度器：阶段1按优先级选请求 + 可行性计算，阶段2分配资源，失败则抢占

## DFlash：并行扩散投机解码（2026-07 归档）
整理自 [[inbox/2026-07-01-推理-DFlash投机解码与slime框架]]
- LMSYS/Z Lab/Modal 联合发布，用轻量扩散模型并行生成候选 token 块，替代 EAGLE 串行瓶颈
- SGLang 已集成；NVIDIA TensorRT-LLM 在 Blackwell 上 gpt-oss-120b 达 5.8× 吞吐
- 智谱开源 slime RL 框架：Megatron+SGLang 原生透传，GLM-5.2 后训练仅 2 天

## LayeredPrefill：分层预填重构 MoE 调度（2026-06-28 归档）
整理自 [[inbox/2026-06-28-推理-LayeredPrefill-MoE服务调度]]
- 首尔大学 MLSys 2026 论文，将模型纵向切分为连续层组，跨层组交错 prefill 与 decode
- 消除 chunked prefill 在 MoE 上的冗余专家权重重载（内存流量 +39%、能耗上升）
- 调度轴从 token 转为 layer，改善 TTFT–TBT 帕累托前沿；开源：github.com/scale-snu/layered-prefill

## P-EAGLE：并行投机解码（2026-07 归档）
整理自 [[inbox/2026-07-02-推理-P-EAGLE并行投机解码]]
- vLLM 推出 P-EAGLE，将 EAGLE-3 串行草稿生成改为单次前向并行生成 K 个候选 token，消除草稿模型自回归瓶颈
- B200 上比 EAGLE-3 快 1.69×，一行配置 `parallel_drafting: true` 即可启用
- 已为 GPT-OSS 120B/20B 和 Qwen3-Coder 30B 提供预训练 head；SGLang issue #23171 跟进中

## vLLM Semantic Router V2 与 Model Runner V2（2026-07 归档）
整理自 [[inbox/2026-07-12-推理-vLLM2026四大更新]]
- Semantic Router 从 v0.1 请求路由进化为 v0.2"系统大脑"，支持多模型动态编排
- P-EAGLE：K=7 时达峰值性能（EAGLE-3 在 K=3 封顶），HumanEval 3.94 vs 3.03
- Model Runner V2 底层架构大重构，vLLM 从推理引擎向推理基础设施平台演进
- 这波更新标志 vLLM 站稳大模型推理基座位置，开始向上层编排延伸

## 最新选型：vLLM vs SGLang（2026-07 归档）
整理自 [[inbox/2026-07-19-大模型推理-vLLM-SGLang-2026选型对比]]
- vLLM 周安装量突破 200 万，v0.19.0 版本 200+ 贡献者；TGI 已进入维护模式
- H100 上 Llama 3.1 8B：SGLang ~16,200 tok/s vs vLLM ~12,500 tok/s（+29%）
- SGLang RadixAttention 在 RAG/Agent/多轮对话场景优势显著
- vLLM 多硬件兼容性仍领先，社区规模约 3× SGLang
- GIE v1.5.0 RC1：支持 EPD 分离式推理调度

## LLM 推理优化三层体系（2026-07 归档）
整理自 [[inbox/2026-07-10-推理-LLM推理优化全景2026]]（2026-07-14 归档）
- 模型层：量化（FP8/INT8/INT4）、剪枝、蒸馏
- 系统层：Continuous Batching、PagedAttention、投机解码、Prefill-Decode 分离
- 应用层：上下文压缩（典型 API 浪费 40-60% 输入 token）、Prompt Caching
- Agent 工作流每次 50-200 次 LLM 调用，推理成本被指数级放大
- 异构计算（CPU-GPU 显存交换）在探索中，混合场景性能可能下降 28%
- 推理引擎商业化加速：Inferact(vLLM) $800M，RadixArk(SGLang) $400M

## SGLang vs vLLM 2026 最新格局（2026-07-25 归档）
整理自 [[inbox/2026-07-25-大模型推理-SGLang-vs-vLLM-2026格局]]
- H100 上 SGLang 吞吐领先 vLLM 29%（16,200 vs 12,500 tok/s），DeepSeek V3 快 3.1×
- RadixAttention 在 RAG/多轮对话场景优势达 6.4×
- DeepSeek V4 Flash/Pro 通过 SGLang Day-0 支持，DSpark 投机解码进一步加速
- 选型建议：默认 vLLM（生态广），高并发/DeepSeek/对话场景切 SGLang

## TGI 进入维护模式 + 投机解码 Red Hat 深度解析（2026-07-14 归档）
整理自 [[inbox/2026-07-14-推理-投机解码生产落地与引擎选型2026]]
- HuggingFace TGI 2025.12 进入维护模式，官方推荐新项目选 vLLM 或 SGLang
- Red Hat 深度解析投机解码：用小模型（0.5-2B）生成候选 token，大模型并行验证，代码/结构化输出场景 3×+ 加速
- vLLM vs SGLang (Llama 3.1 8B, H100)：SGLang ~16,200 tok/s vs vLLM ~12,500 tok/s，prefix-heavy 场景 SGLang 吞吐领先约 30%

## 推理成本优化四步法与网关层（2026-08-09 归档）
整理自 [[inbox/2026-07-30-大模型推理-推理成本优化四步法]]、[[inbox/2026-07-30-大模型推理-PD分离与vLLM收敛]]、[[inbox/2026-07-28-大模型推理-推理网关路由2026]]
- **降本四步法**：连续批处理（GPU 利用率 45%→85%+）→ vLLM PagedAttention（显存利用率 42%→83%）→ 动态稀疏化+早退机制（计算量 -30%）→ Serverless 弹性扩缩容；百万 Token 成本 $30 → $5
- **信通院综述**：PD 分离已成主流；vLLM 在通用引擎中收敛为事实标准（TGI/DeepSpeed/TensorRT-LLM 退居二线）；MoE 成优化焦点——DeepSeek-V3 仅激活 5.5% 参数（37B/671B），DeepEP 专为专家并行优化，KTransformers 等 MoE 专用框架涌现
- **推理网关**：Bifrost 5000 RPS 下延迟仅 11μs（overhead 不再是瓶颈）；路由从静态 fallback 进化到条件路由（user tier/region/cost/latency）；Inworld Router 支持 CEL 表达式 + A/B testing；SiliconFlow 一体化平台 benchmark 快 2.3×、延迟低 32%
- 关联 [[#LLM 推理优化三层体系]]、[[#KV Cache 瓶颈与 TurboQuant 4-bit 量化]]

## vLLM 首届大会 + 前缀缓存决定选型（2026-08-12 归档）
整理自 [[inbox/2026-08-09-大模型推理-vLLM首届大会]]、[[inbox/2026-08-09-大模型推理-SGLang-vs-vLLM]]
- vLLM Conference 8/24-26 落地 Ray Summit；硬件支持扩至 Apple Silicon、昇腾 NPU、Neuron、TPU、Gaudi、昆仑芯、寒武纪 MLU，适配 CUDA 13.0；模型跟进 DeepSeek V4、Qwen3.6、GLM 5.2、Kimi K3、Llama 4 等
- 独立 prompt 场景两引擎接近（H100/Llama 3.3 70B 吞吐 +3.8%）；高前缀复用场景（Agent 系统提示/工具定义）SGLang RadixAttention 比 vLLM 页粒度缓存快约 29%（16200 vs 12500 tok/s）
- 选型估算：前缀命中率 = 重复前缀 token / 总输入 token，命中率高选 SGLang，否则 vLLM（生态广、首发适配快）
- vLLM 生态组件：AIBrix（K8s）、LLM Compressor（量化）、Speculators（投机解码）、vLLM Omni；赞助方含 a16z、Sequoia、NVIDIA、AMD、AWS、阿里云

## vLLM 25K TPS/GPU：Qwen3.5 分离式推理登顶（2026-08-13 归档）
整理自 [[inbox/2026-08-10-大模型推理-vLLM25K-TPS]]
- 8/6 vLLM 官方博客：GB200 NVL72 用分离式 serving 跑 Qwen3.5-397B-A17B-NVFP4，达每 GPU 25K tokens/s 总吞吐
- 核心优化：FlashInfer Blackwell GDN prefill kernel（比旧 FLA/Triton 快 1.02–5.78×，prefill 端到端 +13%、TTFT -12%）+ NIXL 混合缓存传输（同时传 attention KV 与 Mamba SSM 状态）
- 异步调度修复 + 复现配方公开（--async-scheduling），GSM8K 精度 88%；首届 vLLM Conference 8/24-26 落地 Ray Summit
- 关联 [[#Prefill-Decode 分离架构（2026）]]、[[#vLLM 首届大会 + 前缀缓存决定选型]]

## DeepSeek-V4-Flash 正式版公测（2026-08-15 归档）
整理自 [[inbox/2026-08-12-大模型推理-DeepSeek-V4-Flash正式版]]
- 7/31 V4-Flash-0731 上线：仅 130 亿激活参数，Agent 能力反超 V4-Pro-Preview（DeepSWE 7.3→54.4、Cybergym 翻倍至 76.7），接近 Claude Opus 4.8
- 周调用量全球第一（7.22 万亿 token，环比 +13%），缓存命中价低至 0.02 元/百万 tokens；结构尺寸与 preview 一致、仅重做后训练，原生支持 Responses API 并适配 Codex
- 推理侧：MoE 专家权重与 CSA indexer QK 路径引入 MXFP4 量化感知训练，真 FP4 权重推理，国产芯片可稳定部署
- 关联 [[#SGLang vs vLLM 2026 最新格局]]（DeepSeek V4 Day-0 支持）

## 阿里云灵骏真武 M890 超节点：十万亿参数 MoE 推理（2026-08-18 归档）
整理自 [[inbox/2026-08-14-大模型推理-阿里云真武M890超节点]]
- 8/12 上线（首批乌兰察布），64 卡经 ICN Switch 1.0 互联达 800GB/s、显存池 9TB，单实例最高承载十万亿参数级 MoE 推理，国内首个跑通超 2 万亿参数大模型的超节点
- Kimi K3、Qwen3.8 Max（2.4 万亿参数）已通过该实例对外服务；原生 FP8/FP4，Agentic 推理最高 1.5×
- 底层 HPN 8.0 训推一体网络，单集群 13 万卡异构混布、可扩至百万卡；关联 [[AI芯片推理硬件2026全景]]

## 华为 openJiuwen + 昇腾「算力亲和」：Agent 推理首 Token 时延砍半（2026-08-18 归档）
整理自 [[inbox/2026-08-14-大模型推理-华为openJiuwen算力亲和]]
- 华为 2012/云/计算/终端联合打造开源智能体平台 openJiuwen 联合昇腾推「算力亲和」：以 Agent Hint「状态契约」打通 Agent 框架与推理引擎语义通道，让 KV Cache 随任务节奏主动流转
- 首 Token 时延降 57%+、推理存储峰值降 25%，端到端时延 -27.61%、前缀缓存命中率 +33%
- 缓存不再只有"留显存/排队淘汰"，而是按驱逐/卸载/预取在存储层级间主动流动；能力即将开源；关联 [[AI-Agent-2026格局]]

## Qwen 3.8 Max 开源：首个开源权重的 2.4T MoE 旗舰（2026-08-17 归档）
整理自 [[inbox/2026-08-17-大模型推理-Qwen3.8Max万亿MoE开源]]
- 8/13 阿里 Qwen 团队通过魔搭开源 Qwen3.8-2.4T-A95B 权重，首次开源 Qwen-Max 级旗舰：总参 2.4 万亿、每 token 仅激活 95B（约 4%）
- 架构：92 层、隐藏维度 8192、每层 512 专家（激活 10 路由 + 1 共享），原生 256K 上下文可扩至 1M，多步 MTP 训练
- 开源版为纯文本 + 强制思考模式；云端 Max 才有视觉/非思考模式/内置工具；已通过阿里云真武 M890 超节点对外服务
- 关联 [[#阿里云灵骏真武 M890 超节点：十万亿参数 MoE 推理]]

## vLLM Decode Context Parallelism (DCP)（2026-08-23 归档）
整理自 [[inbox/2026-08-19-大模型推理-vLLM解码上下文并行DCP]]
- vLLM 原生支持 Decode Context Parallelism：KV Cache 沿序列维度分片到各 GPU（每卡只存 1/N），破解长上下文下张量并行按 attention head 切分导致的 KV 冗余复制与 GQA/MLA 瓶颈
- 相比标准 TP，长上下文 agent 负载吞吐最高提升 3×；8×B200 跑 Kimi K2.6 达 6,091 tok/s/GPU（并发 512、KV 利用率 82%）
- attention 后立即重配 GPU 摊薄 FFN 权重加载，随上下文长度优雅扩展而非退化；NVIDIA TensorRT-LLM 同方向推 Helix（context+tensor）并行
- 关联 [[#Prefill-Decode 分离架构（2026）]]、[[#vLLM 25K TPS/GPU：Qwen3.5 分离式推理登顶]]

## EAGLE 3.1：修复注意力漂移的投机解码（2026-08-25 归档）
整理自 [[inbox/2026-08-21-大模型推理-EAGLE3.1投机解码]]
- EAGLE 团队 + vLLM + TorchSpec 联合发布 EAGLE 3.1，解决投机解码在长上下文/模板变化下的退化与「注意力漂移」不稳定现象
- 两项架构改动（FC 归一化 + post-norm 隐状态反馈）稳定草稿模型，长上下文接受长度最高达 EAGLE 3 的 2 倍
- Kimi-K2.6-NVFP4 基准：并发 1 时吞吐 2.03×、C=16 时 1.66×
- 关联 [[#EAGLE-3 自推测解码]]、[[#投机解码生产落地综述]]

## NVIDIA Nemotron 3.5 Lightning：面向 Agent 的快速 worker 模型（2026-08-23 归档）
整理自 [[inbox/2026-08-23-大模型推理-Nemotron3.5-Lightning]]
- NVIDIA 8/11 发布 Nemotron 3.5 Lightning（30B 总参/3B 激活 MoE，NVFP4 量化、OpenMDW 1.1 许可），定位长程 Agent 的快速 worker 模型，vLLM Day-0 支持
- 内置多 token 预测（MTP）+ DFlash + DSpark 投机解码默认开启，Thoughtworks 2091 次实测（H200/B200）吞吐 1.46–1.96×
- 定位长程 Agent/子 Agent worker：执行工具调用、校验输出等「执行类」工作，省前沿模型成本；成本 $0.477 → $0.250/百万输出 token
- 关联 [[#投机解码生产落地综述]]

## vLLM 首届大会：State of vLLM 2026 路线图（2026-08-29 归档）
整理自 [[inbox/2026-08-25-大模型推理-vLLM首届大会-State-of-vLLM]]
- 首届 vLLM Conference 8/24–26 与 Ray Summit 同场（旧金山），Woosuk Kwon 主讲 State of vLLM 2026
- 路线图：Flat Model / Model Runner V2 迁移、多级 KV offload（显存→CPU→SSD）分离式 serving、投机解码冲 1000+ TPS + 量化 KV 压缩
- NVIDIA 专场：FlashInfer kernel + Dynamo 分布式全栈优化 DeepSeek/MiniMax；Intel 专场：MXFP8/MXFP4 亚 8-bit + prefill/decode 解耦到专用节点
- 四大赛道：硬件后端、agentic serving、训练、生产推理

## PARS：排序学习近似 SJF 缓解头阻塞（2026-09-02 归档）
整理自 [[inbox/2026-08-29-大模型推理-PARS排序调度降HOL阻塞]]
- ISC High Performance 2026 论文：用 pairwise learning-to-rank（margin ranking loss）预测请求输出长度相对排序，近似最短作业优先(SJF)，缓解 FCFS 头阻塞(HOL)
- 精确预测长度不可行但预测排序可行；无缝集成 vLLM，后台约 1GB 开销，跨多 LLM 与真实数据显著降延迟（含 reasoning 负载）
- Cross-Model PARS 验证跨模型泛化

## vLLM Q3 2026 路线图：冲刺 1000 TPS + Agent 前缀缓存（2026-09-04 归档）
整理自 [[inbox/2026-08-31-大模型推理-vLLM-Q3路线图]]
- `--performance-mode inferactivity` 把 1000 TPS 变成通用目标，补齐 DFlash/DFlare/DSpark 三种 draft 架构；动态投机解码降低验证开销
- 长上下文：滑动窗口/稀疏/混合线性 draft + 量化 KV cache，多种上下文并行
- Agent 前缀缓存：Session-ID/Correlation-ID hints，支持预取/驱逐/选择性 offload
- 支持 KV Events（Mooncake 等 P2P 分布式 KV cache）；AMD 侧补 DSpark + Quark
- 关联 [[#vLLM 首届大会：State of vLLM 2026 路线图]]

## HeteroPanacea：Agentic 推理分离式服务仿真（2026-09-04 归档）
整理自 [[inbox/2026-09-04-大模型推理-HeteroPanacea分离仿真]]
- arXiv 2608.03741 提出分离式服务仿真框架，从①分离量化 ②自动片内/片间并行调度 ③PDAF（prefill-decode-attention-FFN）NPU 异构三个维度做系统级仿真，回答异构系统各组件「该配什么硬件」
- 确认 PD 分离收益：相较传统 GPU 服务吞吐最高 +75%；4 路 PDAF 分离在自定义 NPU 假设下跨模型提升最稳定
- 动因：Agentic 推理的 prefill/decode 行为差异大，单一同构 GPU 难支撑，Vera-Rubin（GPU+Groq LPU）等异构平台成趋势
- 关联 [[#Prefill-Decode 分离架构（2026）]]、[[HBF-NAND-AI推理内存2026]]

## NVIDIA Dynamo 1.0 GA：分离式推理框架量产（2026-09-05 归档）
整理自 [[inbox/2026-09-02-大模型推理-NVIDIA-Dynamo-1.0量产]]
- NVIDIA Dynamo 1.0（GTC 3/16 发布）将 prefill/decode 分离式 serving 推向量产：开源分布式推理框架，支持 vLLM/SGLang/TensorRT-LLM，GB200 NVL72 上最高 7× 吞吐
- 分离式 serving：prefill/decode 拆到独立 worker 池，智能路由 + NIXL 跨节点 KV cache 传输；四大组件 Planner/智能路由器/分布式 KV cache 管理器/NIXL，7.9k stars，Google Cloud 已发布 recipe
- SemiAnalysis InferenceX 基准：DeepSeek R1（FP4）吞吐最高 7×；Grove API 简化 K8s 部署与拓扑感知调度
- 关联 [[#Prefill-Decode 分离架构（2026）]]

## AgentSysBench：Agentic 负载与 LLM 推理的系统级差异（2026-09-08 归档）
整理自 [[inbox/2026-09-04-大模型推理-AgentSysBench表征]]
- arXiv 2608.15127 用 10 个代表性 Agent 应用 + 统一系统级插桩刻画 Agentic 负载与常规 LLM 推理的差异，给出 6 条区分性特征与 4 项可落地优化
- 非 LLM 组件主导延迟（10 个应用里 5 个），会话沙箱内存峰值 28GB；辅助 LLM 调用与上下文开销成「控制面税」
- 优化收益：任务感知服务降延迟 29–40%、通信感知放置最高 4.5×、状态卸载省内存 4.6×、工具结果缓存去 35.2% 冗余搜索
- 关联 [[AI-Agent-2026格局]]、[[#Prefill-Decode 分离架构（2026）]]

## AMPD：多轮推理场景的 PD 分离自适应调度（ICML 2026，2026-09-11 归档）
整理自 [[inbox/2026-09-08-大模型推理-AMPD多轮PD分离框架]]
- 问题：PD 分离已成主流，但多轮推理（agent、迭代检索）中 prefill/decode 交错的工作负载被现有系统忽略，增量 prefill 处理与两阶段静态部署均次优
- 方案：实时按负载自适应决定增量 prefill 在哪执行、如何调度，最大化 SLO 达成率
- 配套规划算法推导 prefill/decode 两阶段的最优资源分配与并行策略，显著优于 SOTA baseline
- 关联 [[#AgentSysBench：Agentic 负载与 LLM 推理的系统级差异（2026-09-08 归档）]]（多轮/Agentic 负载正成为调度研究主战场）
