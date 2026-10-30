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

## DeepSeek V4-Flash-Vision-Exp：V4 家族首个原生视觉模型（2026-09-13 归档）
整理自 [[inbox/2026-09-10-大模型推理-DeepSeek-V4-Flash-Vision-Exp开源]]
- 8/31 以 MIT 协议在 Hugging Face 开源 305B 模型：在文本骨干上叠加视觉编码器 + 对齐层（原生视觉，非外挂 vision tower），保留 DFlash attention / MoE / Hyper-Connections / DSpark 前向路径
- 多模态 Agent 任务逼近 Claude Opus 4.8；README 内置 vLLM/SGLang 启动命令与最小 PyTorch 脚本
- 生态适配快：vLLM 9/2 合并支持（PR 54566），量化版本进入 llama.cpp / Ollama / LM Studio；一周 HF 下载破 20.9 万
- 关联 [[#AMPD：多轮推理场景的 PD 分离自适应调度（ICML 2026，2026-09-11 归档）]]
- 延续 [[#DeepSeek-V4-Flash 正式版公测（2026-08-15 归档）]]：Flash 系列以「小激活 + 全模态」路线抢开源生态
- 关联 [[MLX生态2026]]

## TensorRT-LLM 移除 TensorRT 后端 & vLLM 多级 KV 卸载（2026-09-15 归档）
整理自 [[inbox/2026-09-12-大模型推理-TensorRT-LLM移除TensorRT后端]]、[[inbox/2026-09-12-大模型推理-vLLM多级KV-Cache卸载]]
- TensorRT-LLM 1.2 把 PyTorch 定为唯一执行后端，AOT 编译的 TensorRT 后端彻底移除：`LLM(backend="tensorrt")` 直接抛 ValueError，trtllm-build/refit/prune CLI、各模型 convert_checkpoint.py、tensorrt pip 依赖一并删除（v1.3.0rc20 已预告这是最后支持该后端的版本）
- 含义：NVIDIA 自家引擎转向即时执行 + 运行时 autotuning，与 vLLM/SGLang 的工程模型趋同，shape 特化 engine build 的收益让位于可维护性；1.2 同步新增单节点 DGX Spark 支持（beta）
- vLLM 把原生 KV 卸载从「GPU→CPU DRAM」单跳扩为多级：新增 TieringManager 让主机内存从终点变中间层，二级层可选文件系统 / 对象存储（S3）/ KV Store / 远程节点，被淘汰 KV 重载而非重算
- 跨 TP rank 统一：CPU DRAM 中的 KV 一律以 TP rank 1 的规范形式存放，支持跨 rank 卸载与共享；tier 可标 `locality: LOCAL|REMOTE`，llm-d 的 FS backend 已把 KV block 落成共享文件系统文件实现跨副本复用
- 意义：长上下文 / Agent 多轮负载下，缓存容量与命中率取代算力成为吞吐主变量——与 [[#AgentSysBench：Agentic 负载与 LLM 推理的系统级差异（2026-09-08 归档）]] 的「控制面税」互为因果

## vLLM v0.29.0：Model Runner V2 成为全模型默认执行路径（2026-09-17 归档）
整理自 [[inbox/2026-09-14-大模型推理-vLLM-v0.29-MRV2全覆盖]]
- 594 commits / 277 贡献者：MRV2 从池化模型扩展到所有模型的默认执行路径，数月执行引擎重构基本收口（MRV1 仅保留给少数 ROCm 与未支持特性）——默认路径切换比零散优化更能解释产能跃升
- MRV2 补全：CUDA graph 显存剖析（KV cache 自动 sizing）、batch-sharded sampling（每步 logits 显存降 1/TP）、prompt embeds、extract_hidden_states 投机
- 新模型批量接入：腾讯 Hy4-preview（770B/49B-active MoE + Gated DeepSeek Sparse Attention）、Qwen3.8-Flash-Next、Kimi K3 NVFP4、NemotronH-Omni-Reasoning-V3
- K3/V4 调优：K3 latent tail 融合 MXFP4 top-k 终局约 -5% E2E 延迟；K3 Mamba 元数据合并为单次 Triton launch，kernel 提速 6.6–7.6×
- 关联 [[#TensorRT-LLM 移除 TensorRT 后端 & vLLM 多级 KV 卸载（2026-09-15 归档）]]

## KV Cache 的「互联网」：把网络与存储当一级推理资源（2026-09-17 归档）
整理自 [[inbox/2026-09-14-大模型推理-KV-Cache的互联网]]
- 立场（arXiv 2608.01526，Siddhant Ray / Nick Feamster / Junchen Jiang）：LLM 推理已变成跨云、跨数据中心的全局负载，KV 复用把问题从「算力-存储权衡」升级为「互联网级内容分发」，推理端只是 CDN 上的一个节点
- 主张解耦计算与 KV 存储：模型侧指标（命中率/压缩率）+ 基础设施指标（带宽/延迟/价格）+ 应用指标（SLO/请求模式）联合决定 recompute / store / reuse
- 割裂现状：Mooncake、LMCache、HiCache 等 KV 层与模型侧 KV 压缩进展互不相通，缺少跨边界抽象；测度、抽象与架构均待建设
- 方向：把网络与存储从被动管道变成主动可调旋钮，与 [[#vLLM v0.29.0：Model Runner V2 成为全模型默认执行路径（2026-09-17 归档）]] 所属的引擎侧多级分层互为补充
- 关联 [[HBF-NAND-AI推理内存2026]]

## BOOST：让主机内存与 HBM 带宽被并发吃掉（2026-09-19 归档）
整理自 [[inbox/2026-09-16-大模型推理-BOOST-主机内存与HBM并发访问]]
- arXiv 2609.13592（MIT，NVIDIA / VMware 参与）：既有 serving 把主机内存当容量后备——装得下就只用 HBM，装不下就预取，而预取会抢占 HBM 写带宽；BOOST 让同一波 GPU threadblock 按带宽比例同时访问两层，静态权重用 modulo 页放置消除访问比例方差，动态 KV 页池做 wave-aware 管理，且不改 kernel
- Grace Hopper + vLLM 实测：等 batch TPOT 提升 4.3%（预取反而退化 6%）；高吞吐场景吞吐 +31%、比预取高 15%
- 判据：DDR / HBF / CXL 应被当作「可并发共享的带宽资源」而非容量后备，与 [[#全栈实测：HBF 当 SSD 平替做 KV 卸载反而更慢（2026-09-14 归档）]] 方向一致但落点不同——前者要求并发比例对齐带宽比，后者说明瞬态 KV 不满足「读多写少」前提
- 同批另一条路线 WriteScope / InplaceKVCache（arXiv 2609.14507，国防科大）：单卡跑 MoE 长上下文必须把 KV 溢写到 CPU，而溢出的 KV 有两种用法（传回 GPU 做 attention / 在 CPU 原地计算）要求相反的物理状态；InplaceKVCache 让「每个字节写时就固定物理驻留位置」，于是 CPU-GPU 负载均衡不再搬数据、退化为纯调度问题
- WriteScope 用两维四区布局（设备亲和 × 访问模式）沿序列维切 CPU/GPU 份额，roofline 模型定最优 CPU 份额并在线跟踪 CPU 成本漂移；长上下文（≥8K）较 4 个复现基线几何平均加速 1.5–2.5×（A100）、1.4–1.7×（V100），32GB 显存下支持 1M token 聚合规模，且已验证与 DeepSeek-V4-Flash 原生稀疏注意力可叠加
- 关联 [[HBF-NAND-AI推理内存2026]]

## 负载感知的 Prefill 偏转（arXiv 2607.02043，2026-09-18 归档）
整理自 [[inbox/2026-09-18-大模型推理-Prefill-Deflection负载感知调度]]
- PD 分离的新不对称：突发重尾负载下 prefill 节点打满、decode 节点算力闲置；2P2D A100 生产式集群上 P95 TTFT 中真正的 prefill 执行只占 2–23%，其余全是排队与跨节点 KV 传输
- 方案：decode 节点把排队请求的 prefill 以 chunked-prefill 步骤插进自己「在飞」的 decode 批，逐请求估算两条路径的 TTFT，只在 decode 路径确实改善尾延迟且不破坏 TBT SLO 时才偏转
- 收益主源：被偏转请求的 prefill 就地运行，跨节点 GPU-GPU KV cache 传输被直接消除
- 实测（vLLM + DeepSeek-V2-Lite + 生产 trace）：P95 TTFT 最高降 81%、SLO 达成率最高 +79%、路由开销亚毫秒级
- 意义：「分离」不是硬约束，按瞬时负载动态搬运 prefill 比静态 P/D 配比更能守住尾延迟 SLO

## KV Cache 作为 Agent 运行时（Yandex Research，2026-09-18 归档）
整理自 [[inbox/2026-09-18-大模型推理-KV-Cache作为Agent运行时]]
- 立场：交互能力有一大块是推理运行时问题，不必改权重——KV cache 是模型的「活动执行状态」，改 cache 结构即可实现训练时不存在的新交互协议
- 技巧：利用 RoPE 相对位置性质 ρ(q,i_q)ρ(k,i_k)ᵀ=ρ(q,i_q−i_k)kᵀ，cache block 只按 block-local 坐标存一份，查询时对当前 query 施加 block 专属旋转 → 同一物理块在不同流里呈现不同逻辑偏移
- Hogwild! Inference：并发运行同模型多实例并写共享注意力内存，worker 实时看到彼此的部分生成；AsyncReasoning 拆输入/私有思考/公开输出三流，首个非思考 token 时间最高降 80×
- 抽象落点：流=进程、cache block=共享内存、attention view=地址映射、模式切换=调度事件、工具更新=中断；已在 Doom 上用免训练 Qwen3.5 验证
- 关联 [[AI-Agent-2026格局]]

## GPT-Live-1 进 API：全双工语音按秒计费，推理外包给后端模型（2026-09-19 归档）
整理自 [[inbox/2026-09-19-大模型推理-GPT-Live-1全双工语音API]]
- OpenAI 2026-09-10 把 ChatGPT 的全双工语音模型放进 API：model id `gpt-live-1`、端点 `v1/live/sessions`，边听边说、自带打断与背景噪声处理，定价 $0.05/分钟且按秒计费
- 架构：语音层只做对话，reasoning 与工具调用委派给你指定的 backend 模型——「想」的时候对话可以继续，这是对 Realtime API（单模型兼做语音+推理、按音频 token 计费）的正面替代
- 双层账单是关键结构：语音层 $0.05/分钟 + 后端推理模型按自身 token 费率 + 工具/电话线/存储/应用基建，总成本是两项相加而非单一数字
- 接入面：WebRTC、WebSocket、sideband 服务端控制、telephony/SIP；输入输出支持文本+音频，不支持图像/视频；无免费额度，按并发会话限流
- 与 per-token 计费对比：Realtime（gpt-realtime-2.1）旗舰约 $0.06–0.11/分钟（未缓存长通话可达 $0.18–0.46），按分钟计费把「说多久」的成本变得可预测
- 工程含义：语音 Agent 的成本瓶颈从音频 token 变成「会话时长 × 后端推理量」，并发会话数成为新的配额单位
- 对本地语音服务（Qwen3-ASR+TTS/MLX）的对照：云侧用 per-second 计时把交互延迟卖成钱，本地路线的价值锚点就是零边际成本 + 隐私；两层架构（轻前端 + 大后端）本地同样可复刻
- 关联 [[MLX生态2026]]、[[AI-Agent-2026格局]]

## MICRO 2026 · DOPS：PD 分离不够，异构推理要按算子动态调度（2026-09-21 归档）
整理自 [[inbox/2026-09-21-大模型推理-MICRO2026-DOPS动态算子调度]]
- 北大等（Bonan Yan 组）指出 PD 分离 + roofline 算子放置在真实系统中不够用：端到端时延还取决于 workload shape、运行时设备争用与权重布局，论文被 MICRO 2026 录用
- 两个组件：Bifocal 调度器（把算子动态放置到设备，构建 stage-aware DAG）+ Weight Layout Arbiter（在严格内存约束下选择硬件友好的权重布局）
- 相对 PD 基线几何平均加速 1.20–2.23×，叠加 WLA 再获 1.28–1.33×；目标系统为 NPU + PIM 异构
- 核心论点：权重布局决定可用并行度与访存模式，与算子调度必须联合优化，分开调会互相掣肘；既有 PD 假设「prefill 计算密集 / decode 内存密集」在真实负载形状下会失效
- 与 [[#负载感知的 Prefill 偏转（arXiv 2607.02043，2026-09-18 归档）]] 同向：静态 P/D 划分正在被运行时闭环调度取代
- 关联 [[MLSys2026论文精选]]、[[AI编译器2026格局]]

## KVServe 与 vLLM v0.30 Fast Start：KV 压缩与权重驻留都变成运行时决策（2026-09-26 归档）
整理自 [[inbox/2026-09-23-大模型推理-KVServe服务感知KV压缩]]、[[inbox/2026-09-23-大模型推理-vLLM-v0.30-Fast-Start权重缓存]]
- KVServe（SIGCOMM 2026，arXiv 2605.13734，中科院大学 + 计算所 + 上交）：解耦推理把 KV 从 GPU 内部状态变成跨网络与存储边界的显式载荷——KV 传输最多占作业完成时间（JCT）的 60%（32K 请求跑 Qwen3-235B、64 节点 prefill 集群时 KV 出口带宽需求达 2.1Tbps）→ 主张压缩不是写死配置，而应随服务状态动态选择策略
- 三段式设计：统一压缩策略空间（含新组件与跨方法重组）→ 离线 profiling 生成 profile → 在线 Service-Aware Controller（延迟模型 + 轻量 bandit）在约束下选 profile 并纠正 offline-online 失配；已集成 vLLM，PD 分离下 JCT 最高 9.13× 加速（≤5Gbps 受限带宽下 9.2×），KV 解耦 + 远程前缀缓存下 TTFT 最高降 32.8×，且始终守住严格 SLO
- 关键设计价值：带宽充裕时自动识别「省下的通信抵不上算力成本」而绕过压缩——静态方法的典型翻车点；精度上 MixHQ 量化器 + 贝叶斯搜索在最高 10× 压缩下相对精度保持 99% 以上
- vLLM v0.30.0（762 commits / 315 贡献者）：Fast Start 用常驻的 per-GPU weight-cache 守护进程把已量化、已按 TP 切分的权重留在显存，重启的 engine 经 CUDA IPC 直接映射，省掉一次完整权重加载（大 MoE 上是分钟级差距）——优化的是重启成本而非稳态吞吐，覆盖引擎升级、崩溃恢复、弹性扩缩容
- 破坏性变更：scale-out 端点需显式加 `--enable-scale-out`，否则该路径不生效，升级前要过配置审计；同版本还含 watermarking、Model Runner V2 延续、HiSparse 与 Kimi K3 相关改动
- 主线收敛：一处优化**权重常驻**（Fast Start）、一处优化**KV 载荷的跨层传输**（KVServe / [[HBF-NAND-AI推理内存2026]] 的 SALT-KV），与 [[#MICRO 2026 · DOPS：PD 分离不够，异构推理要按算子动态调度（2026-09-21 归档）]] 的「静态划分让位运行时闭环」同向

## KV Cache 卸载落地实测：ODCC 全场景测评与华为 OceanStor M900（2026-09-25 归档）
整理自 [[inbox/2026-09-25-大模型推理-KVCache卸载ODCC全场景测评]]、[[inbox/2026-09-25-大模型推理-华为OceanStor-M900上下文存储]]
- ODCC AI 存储实验室（联合 NVIDIA / XSKY MeshFusion / 大普微）测评：KV Cache 卸载到 SSD 池后，长序列场景 TTFT 从数十秒降到百毫秒级，TPS 提升约 10–32×（Cache 命中时 7 万–11 万 tokens/s）；数据路径基于 BlueField-3 DPU + JBOF，绕开 host CPU/内存直达显存，形态对齐 NVIDIA CMX
- 两个反直觉数据：EC 4+2:1 纠删码与单副本性能基本持平（打破「EC 必然拖慢」的旧认知，可放心换容量利用率）；Spectrum-X 下集群 EC 顺序读 272.1 GiB/s，达三存储节点物理极限的 97.4%——网络不是瓶颈
- 真门槛在语义不在带宽：KV 张量带位置信息、移动即失效，缓存层必须介入调度决策，故障还可能表现为静默错误（呼应 [[MLSys2026论文精选]] 的静默失败线）
- 华为 OceanStor M900 Context Memory Storage（全联接 9/17）：为 SuperPoD 提供 PB 级、TB/s 级全共享内存空间，在片上内存 / DRAM / SSD 之间做 KV Cache 分层与调度，灵衢一跳直连，定位 L3.5 层；官方叙事是 AI 基础设施从「算力为中心」转向算力/网络/存储深度协同
- 与 [[HBF-NAND-AI推理内存2026]] 的 SALT-KV、[[#KVServe 与 vLLM v0.30 Fast Start：KV 压缩与权重驻留都变成运行时决策（2026-09-26 归档）]] 同线：KV 分层从硬件规范落到运行时可观测、可调度的软件方案
- 关联 [[HBF-NAND-AI推理内存2026]]、[[MLSys2026论文精选]]、[[AI芯片推理硬件2026全景]]

## Agentic 服务的 KV 预测式管理与混合注意力三视图缓存（2026-09-27 归档）
整理自 [[inbox/2026-09-27-大模型推理-Agent服务KV缓存CacheScout]]、[[inbox/2026-09-27-大模型推理-SGLang-DeepSeekV4百万上下文优化]]
- CacheScout（arXiv 2608.14624，UCSC/UW/芝大）：针对 LLM 自主决策、执行路径事先不可知的 agentic 工作流，在线学习 Agent 之间转移概率，据此决定 KV 的保留/淘汰与预取；与 KVFlow 的分界在后者要求静态执行图 → KV 管理从「按前缀命中率」转向「按未来执行概率」
- ShadowRadix（SGLang，DeepSeek V4 Day-0 支持）：V4 注意力从单一 MLA 变为三种异构并存——SWA（滑窗 128）/ CSA（4:1 压缩 + TopK 稀疏）/ HCA（128:1 高压缩），各自 KV 生命周期与索引逻辑不同，压缩机制还引入跨 token 依赖
- 实现路径：在 RadixTree 上扩虚拟地址表，每 token 一个虚拟位置，除以 4 / 除以 128 映射到三张 Shadow 页表；SWA 用 Tombstone 自动回收滑窗外过期 KV，压缩支路因压缩比固定无需淘汰；两个 Ring Buffer 维护 Compressor 中间状态
- 读法：混合注意力模型的缓存管理已从「分页」变成「多视图地址翻译 + 依赖追踪」，与 [[HBF-NAND-AI推理内存2026]] 的分层放置、[[AI-Agent-2026格局]] 的执行路径预测同向
- 关联 [[MLSys2026论文精选]]、[[AI编译器2026格局]]

## vLLM 0.30.0：分层 KV 卸载原生化 + KV 布局重构（2026-09-29 归档）
整理自 [[inbox/2026-09-29-大模型推理-vLLM0.30与分层KV卸载]]
- 9/10 官方博客说明原生分层 KV 卸载设计（v0.22 起内置、无外部依赖），9/22 发布 0.30.0；KV cache 布局做了一次重构——把 K/V 打包进 content 维（backend-published KV packing），并支持按 KV-cache group 选择不同 attention 后端
- 核心设计 host-centric：所有 KV 都经 CPU DRAM，host 侧用规范化布局（每页存一层的一块、跨 TP rank 的 KV head 聚成连续区），于是不同加速器、不同注意力后端、不同并行配置共享同一份 KV
- 二级层带来跨节点能力：缓存水平扩展、新实例用共享存储热启动、PD 分离或负载均衡时对等传输 KV；混合模型上 Mamba 状态层每 chunk 覆盖 token 远多于全注意力层 → 卸载频率更低（与 SGLang UnifiedRadix/HiCache 同构）
- 调度侧同期改动：KV-cache watermark 降抢占、two-phase allocation 处理跨组前缀命中、Marconi 式混合缓存准入、Mamba/线性注意力前缀缓存保留、LIFO 空闲块复用顺序修复 → KV 管理从「一个池子」变成有策略的分层资源
- 读法：引擎竞争已从「谁的 attention 快」转到「谁能把 KV 的放置、共享与生命周期管明白」——本条补分层卸载设计，与 [[#KVServe 与 vLLM v0.30 Fast Start：KV 压缩与权重驻留都变成运行时决策（2026-09-26 归档）]] 的 Fast Start 同属 v0.30 版本线

## 推理控制平面与共享 KV 溯源（2026-10-01 归档）
整理自 [[inbox/2026-10-01-大模型推理-llm-d-v0.10与推理控制平面]]、[[inbox/2026-10-01-大模型推理-共享KVCache溯源]]
- llm-d v0.10.0（9/29）+ BU 综述（arXiv 2609.23130）：主张主战场从「引擎内优化」转为「分布式控制问题」——vLLM/SGLang 是执行引擎，llm-d 是引擎无关的推理控制平面（路由、缓存状态感知、分离、流控、自动扩缩、可靠性），互补而非替代
- 核心判据：稀缺资源从裸 FLOPs 转向可复用状态（KV）、阶段放置、异构加速器与网络；v0.9 起路由高可用、DisaggregatedSet revision 路由让 P/D 滚动升级首次安全、GPU 利用率感知路由；路线扩散到扩散模型路由、非 K8s 环境（RL/Slurm/文件系统端点发现）与 GB200/GB300/XPU/ROCm/TPU well-lit paths
- 共享 KV 溯源（arXiv 2609.38706，Griffith/NUS/Macquarie）：前缀缓存已是默认优化但缓存块「来源」（哪个租户、哪次请求写入）通常不记录；共享带来 PromptPeek / InputSnatch 等 TTFT 计时侧信道攻击 → 溯源须与路由、淘汰、跨实例 KV 搬运联合设计
- 工程风险提示：vLLM 分离式预填仍标 experimental；llm-d 月更节奏 + 仓库迁移/弃用，需锁版本并预留升级工时
- 关联 [[HBF-NAND-AI推理内存2026]]、[[MLSys2026论文精选]]

## Herschel：生产推理的「按需剖析 + 持续优化」闭环（2026-10-06 归档）
整理自 [[inbox/2026-10-03-大模型推理-Herschel生产推理按需剖析]]
- 阿里团队（arXiv 2609.40247，cs.OS）：用「自适应按需剖析」替代 always-on tracing——只在有界诊断窗口（典型 30s）内挂载全栈采集，同时拿到证据完整度与可接受开销
- 三组件分工：Hprof（运行时动态挂载/按需配置采集）、Analyzer（重建执行关系、定位低效点）、Optimizer（驱动 AI agent 生成改动，经专家评审后受控验证再上线）
- 实测开销：TTFT <0.5%、TPOT 约 7%（预填/解码同机部署时 TPOT 影响最明显），仍比 PyTorch profiler 全量低 20–30%（最大测到 235B）
- 定位：属推理「控制面/运维」层，与引擎内优化互补——线上问题靠执行证据闭环，而非只看平均吞吐

## 面向 KV Cache 的「互联网」：计算与 KV 存储解耦到跨云（2026-10-06 归档）
整理自 [[inbox/2026-10-03-大模型推理-KVCache-Internet解耦计算与状态]]
- UChicago 立场论文（arXiv 2608.01526，Siddhant Ray / Nick Feamster / Junchen Jiang）：agent、检索、工具调用等负载上下文高度重叠，KV 复用机会正把 KV Cache 变成跨云分发对象，网络应成为一等公民
- 现状割裂：模型侧压缩（缩 KV）与系统侧传输优化各自演进，被传统云/数据中心边界挡住，复用收益无法在全局兑现
- 主张：解耦「计算」与「KV 存储」并按内容分发系统（CDN）范式设计 KV 管理——带宽、时延、价格直接决定「搬运 vs 重算」；需通信/策略/数据发现三件套（lookup 服务发现可复用状态、估算传输 deadline 与可用带宽）
- 可衔接原语：前缀缓存（发现可复用）、KV offloading（跨层级搬）、预取与路由（放置决策）、Internet 式路由；对内存层级的意义是 KV 成为横跨 HBM / CXL / 本地 SSD / 远端存储 / 网络的统一寻址对象，归属与一致性域问题被推到台前
- 关联 [[HBF-NAND-AI推理内存2026]]

## SGLang 分层 KV 原生化 + K8s 推理服务冷启动（2026-10-08 归档）
整理自 [[inbox/2026-10-05-大模型推理-SGLang分层KV缓存与2026路线图]]、[[inbox/2026-10-05-大模型推理-K8s推理服务冷启动与SLO调度]]
- SGLang 把 KV cache 分层做成原生能力：GOSIM Shenzhen 2026（10/17）专设工作坊讲 RadixAttention / Unified RadixAttention、HiCache 跨存储层级搬 cache、面向混合注意力模型的 Unified Memory Pool
- 2026 Q2 路线图主目标：分层缓存与混合注意力原生化、为 agentic 负载提供弹性 session 控制；Distributed KVCache System for Agentic Workload（#21846）、HiSparse（#28874）、elasticmem 集成
- 调度侧同期重构：forward-mode 泛化、scheduler 更无状态、完全支持 mixed chunked prefill、把准备逻辑更多挪进 CUDA graph 以降 CPU 开销
- 生产侧佐证：LMCache 的「多进程 KV cache」与 KV 持久化到 S3（NIXL / ICMS / Mooncake 一线）已成独立赛道
- K8s 侧缺口（arXiv 2607.16596 / 2609.20874）：缺「一等公民的模型/权重分发」，靠 sidecar 与镜像层拼凑 → 冷启动 2–10 分钟，响应式扩缩「结构性地总是晚一步」
- 扩缩信号在进化：队列深度 / KV cache 利用率 → Cascade（arXiv 2608.06557）按同一 SLO 下请求紧迫度分层调度；生态面 Gateway API Inference Extension、NVIDIA Dynamo、ModelExpress 权重分发、KAI Scheduler / Grove / Kueue
- 关联 [[#llm-d：Kubernetes 原生分布式推理控制平面（2026-08-31 归档）]]、[[HBF-NAND-AI推理内存2026]]

## KVSET 工作集容量规划 + vLLM v0.31.0 重启/背压/准入（2026-10-10 归档）
整理自 [[inbox/2026-10-07-大模型推理-KVSET工作集容量规划]]、[[inbox/2026-10-07-大模型推理-vLLM-0.31发布]]
- KVSET（arXiv 2609.27746，Luchang Li 等，9/23）：把「KV cache 工作集」定义为达到目标命中率所需的最小缓存容量；用 Mattson stack algorithm 一次算出各容量下的命中率（对每个 KV 页算 LRU 栈距离，再与各候选容量的页数比较判定命中），由「目标命中率所需前缀页的最大 LRU 深度」直接反推最小缓存容量，把经验参数变成可测指标
- 价值定位：KV 分层/卸载方案（[[HBF-NAND-AI推理内存2026]] 的 SALT-KV、vLLM 多级 KV 卸载）缺的正是「该配多大」的独立判据；已用生产 LLM 负载 trace 验证，开源且同时支持在线请求与离线回放
- vLLM v0.31.0（10/5，717 commits / 307 贡献者）：结构信号在三处——`vllm preload` 权重缓存守护进程把已量化权重常驻显存跨引擎重启复用（实验性 `vllm snapshot create/restore` 用 CRIU 恢复 TP1 引擎）；KV offloading 新增**背压检测**（#50045）；`--max-num-active-seqs` 把 RUNNING 准入与 `max_num_seqs` 解耦，`--long-prefill-token-threshold` 改为按等待 prefill 数量自适应、等待队列重做（已持有 KV 块的请求优先调度）
- 内核与并行主线（延续 V4.1-Flash 优化）：FlashMLA mega attention + NVFP4 压缩 KV cache 成 SM100 默认、Mega-Gate 融合门控 GEMM、decoder 边界融掉 TP all-reduce 与 MoE finalize；MoonEP 均衡 EP all2all、DeepEPv2 + 序列并行、EPLB 共享专家重叠
- 安全/兼容：per-request 多模态 kwargs 默认拒绝（需 `--trust-request-mm-kwargs`）；prefix-cache extra key 按来源打标签（LoRA 名与 `cache_salt` 不再相撞）、LoRA 路径进 block hash；移除 `tokenizer_mode="slow"`、`--enforce-eager` 同时禁用 JIT warmup、XPU graphs 默认开启
- 读法：延续 [[#KVServe 与 vLLM v0.30 Fast Start：KV 压缩与权重驻留都变成运行时决策（2026-09-26 归档）]] —— 引擎优化对象从稳态吞吐扩到「重启成本、卸载背压与队列准入」这些运维面
- 关联 [[#vLLM 0.30.0：分层 KV 卸载原生化 + KV 布局重构（2026-09-29 归档）]]、[[MLSys2026论文精选]]

## PD 分离扩到多厂商 GPU + vLLM×AgentX 分布式 KV cache（2026-10-12 归档）
整理自 [[inbox/2026-10-09-大模型推理-PD分离多厂商GPU]]、[[inbox/2026-10-09-大模型推理-vLLM面向agentic服务]]
- P-D 分离（DPD）的基本判断：prefill 算力受限（决定 TTFT）与 decode 带宽受限（决定 ITL）共置会互相干扰，长 prompt 会拖住所有并发请求的 token 生成；分离后两池各自对准瓶颈，代价是池间 KV 搬运带宽成为新瓶颈
- 机制与落地：chunked prefill 让单个 prefill 请求能拼出多个 decode max batch，最大化「可捎带 decode」的覆盖；落地形态从 NVIDIA 单厂商扩到多厂商 GPU，AWS SageMaker HyperPod 用 vLLM 实现 DPD、池间走 EFA RDMA 而非 NVLink，把耦合从专有硬件解到标准网络
- 谱系与判据：Splitwise（map-reduce 式阶段拆分）/ DistServe（OSDI'24，goodput）/ Mooncake（KV 中心化）/ MemServe（弹性内存池）；DPD 不是万灵药，只有长 prompt + 高并发共置才明显收益
- vLLM × AgentX（2026-09-08）：把 Mooncake Store 集成成**分布式 KV cache 池**，让 prefix 缓存超出单机显存并跨每个 engine 复用，KV cache offloading 成为 first-class；补齐稀疏 / 压缩 / 线性注意力架构支持的同时，异步调度、P-D 分离、投机解码与并行均不退化
- 新架构坑：Kimi K3 的 MLA + Kimi Delta Attention 把 KV 压进单一 latent 空间，纯张量并行会在各 rank 复制该 latent cache → 需要新的并行策略；趋势与 [[#KVSET 工作集容量规划 + vLLM v0.31.0 重启/背压/准入（2026-10-10 归档）]] 一致：KV 从单机显存管理升为跨 engine / 跨层的分布式缓存系统
- 关联 [[#KVSET 工作集容量规划 + vLLM v0.31.0 重启/背压/准入（2026-10-10 归档）]]、[[AI芯片推理硬件2026全景]]

## PrfaaS 跨数据中心 prefill 外包 + Tutti GPU 发起 I/O（2026-10-11 归档）
整理自 [[inbox/2026-10-11-大模型推理-PrfaaS跨数据中心KV缓存路由]]、[[inbox/2026-10-11-大模型推理-Tutti-GPU侧发起IO的SSD-KV缓存]]
- PrfaaS（arXiv 2604.15039，Moonshot AI + 清华）：新模型 KV 占用变小后瓶颈从显存容量转向算力/带宽错配，于是把长请求 prefill 整体外包给算力密集的远端集群、KV 经以太网回传 decode 节点；核心是路由阈值 t（增量 prefill 长度 l > t 才外移），案例最优 t=19.4K token → 49.6% 请求外移、出口约 13 Gbps（占 100 Gbps 链路 13%）
- PrfaaS 实测（内部 1T 模型 Kimi Linear，均输入 27K/输出 1024）：32×H200 远端 prefill + 64×H20 本地 PD vs 96×H20 同构基线——TTFT −50%、P90 −64%、吞吐 1.54×（朴素异构仅 1.16× → 增益主要来自调度层）；坑点是混合前缀缓存池需单独处理线性注意力/SWA 层的请求级 recurrent state（匹配规则与 token/block 级不同）
- Tutti（arXiv 2605.03375）：长上下文把 KV 推往 NVMe SSD 后，GPU 显存布局碎片化会产生海量微小随机 I/O，即使走 GPU Direct Storage 每个 I/O 仍要 CPU 发起 → 低并行度 CPU 成瓶颈；Tutti 改以 GPU 为中心的 KV 对象存储打通 HBM↔SSD（gio_uring 提交/完成环驻留 GPU、按依赖事件启动 I/O，离线分析各层空闲窗口做查表调度、写延后到空窗），受控固定 32K prompt 下 GPU 停顿近零；跨节点仍走 CPU + RDMA（GPU-initiated RDMA 列为 future work）
- 关联 [[#PD 分离扩到多厂商 GPU + vLLM×AgentX 分布式 KV cache（2026-10-12 归档）]]、[[HBF-NAND-AI推理内存2026]]

## KV 的取数粒度：SAC（CXL 细粒度）与 DirectKV（NVLink-C2C 零拷贝）（2026-10-16 归档）
整理自 [[inbox/2026-10-13-大模型推理-SAC-CXL稀疏注意力KV]]、[[inbox/2026-10-13-大模型推理-DirectKV零拷贝KV卸载]]
- **SAC**（arXiv 2606.19746，作者含北大 Guojie Luo 等）：首个面向稀疏注意力模型的分离式 KV cache——RDMA 池按前缀整块抓 KV 再 decode 对稀疏注意力极度浪费（decode 只激活少量 KV）；SAC 改为 KV 全量驻留 CXL 池、用 cache-line 粒度 load/store 按需取 top-k；DeepSeek-V3.2 + SGLang 实测相对 RDMA 基线吞吐 2.1×、TTFT 降 9.7×、TBT 降 1.8×，实现基底是 SGLang 的 HiSparse 稀疏服务路径
- **DirectKV**（OSDI'26，UVA 的 Shutian Luo / Haiying Shen）：首个面向 GH200/GB200 的零拷贝 KV offloading——GPU kernel 直接访问 CPU 内存里的 KV，取消 HBM staging buffer，CPU 内存成为 KV 容量的直接扩展
- DirectKV 关键测量：朴素零拷贝在 PCIe 上慢 20×+、即便 NVLink-C2C（900GB/s）上仍慢 2×——matmul kernel 按 HBM 常驻张量调优，反复从 CPU 取数还会压低 L2 命中率；解法是用 SMEM tiling 重构访存 + CPU-memory-aware kernel，并把 KV 生成与 attention 融合进单 kernel 做 warp 级流水（GH200 上传输量最多 -50%、显存 -43%、端到端最高 1.2×）
- 读法：与 [[#BOOST：让主机内存与 HBM 带宽被并发吃掉（2026-09-19 归档）]] 同线——KV 的**取数粒度**（block / cache-line / on-demand top-k / 并发比例）正成为继「放哪一层」之后的新设计维度
- 口径：DirectKV 摘要与引言已读原文 PDF，性能数字为论文自测、未复现；SAC 机构页未在 abs 摘要列出，作者单位未核实
- 关联 [[HBF-NAND-AI推理内存2026]]、[[MLSys2026论文精选]]

## vLLM vs SGLang 选型判据：先量前缀重合率（2026-10-15 归档）
整理自 [[inbox/2026-10-15-大模型推理-vLLM与SGLang选型判据]]
- Red Hat 开发者博客（10/14）+ 第三方基准给出同一判据：提示词互不重复的批量任务上两者吞吐差在 5% 以内；一旦请求共享前缀（系统提示、RAG 文档、工具定义）比例 >60%，SGLang 的 RadixAttention 延迟优势明显
- 决定因素是负载画像而非引擎强弱：前缀重合率高 → 缓存复用收益大；以唯一提示词为主 → 比模型支持广度与运维复杂度
- 工程坑比能力更重要：Qwen3.8-27B 类混合模型在 vLLM 0.30 需下调序列上限才能起、SGLang 0.5.20 曾静默限并发 20、TensorRT-LLM 稳定版在部分新架构上直接不加载
- PD 分离已成默认 playbook（DistServe 回顾），多轮场景出现 PPD/AMPD 变体（arXiv 2603.13358 / 2602.14516），把增量 prefill 与首次 prefill 区别对待
- 口径提示：`inbox/2026-10-15-大模型推理-OSDI26零拷贝KV卸载`（USENIX 页 luo）即上文 DirectKV，同文未重复立项；`inbox/2026-10-19-大模型推理-DirectKV零拷贝KV卸载`（2026-10-19 采集）亦为同文第三次采集，仅补充 CUDA Graph 可 replay（零拷贝让动态 KV 块选择只改 kernel 入参、不改图拓扑）一条，已并入上文

## llm-d 0.10 生产化收口 + agentic MTP 并发前沿（2026-10-20 归档）
整理自 [[inbox/2026-10-17-大模型推理-llmd0.10与PD分离生产化]]、[[inbox/2026-10-17-大模型推理-GLM5.2-agentic负载MTP与KV卸载]]
- llm-d 0.10 主题从「加功能」转为「可运维」：签名发布镜像、更安全的 rollout，并给每条 well-lit path（含 1P1D）发布测试矩阵，底座绑 vLLM 0.30；PD 分离已是默认服务形态（官方口径 3× 输出吞吐、2× TTFT，项目自报基准）
- 分离落地件：prefill 池 + decode 池共用一个 InferencePool，靠 `llm-d.ai/role`（prefill/decode/prefill-decode）路由；P/D 可用不同 TP 度——decode 吃带宽需更大 TP，prefill 用小 TP 更省；KV 接线走 vLLM 原生 `OffloadingConnector`（CPU/共享 FS）或 LMCache/Mooncake connector 做跨 pod P2P
- agentic 负载实测（SemiAnalysis Weka trace 的 219 个生产 Claude Code 会话，H200 + GLM-5.2-FP8，1P1D/DEP16/32 GPU）：以「每用户 ≥30 tok/s」为底线，MTP 能撑并发 128、聚合约 5,647 tok/s，而只靠 CPU 侧 KV 卸载在并发 16 撞墙（约 561 tok/s）→ ≥8× 并发、10.1× 聚合输出
- 优化顺序：先用推测/并行解码拉高每用户出词率，再谈靠卸载扩并发；单靠卸载只解决容量不解决速率——与 [[#KV Cache 卸载落地实测：ODCC 全场景测评与华为 OceanStor M900（2026-09-25 归档）]] 的「容量 vs 速率」判据一致
- 口径：两组数字均为项目自报、单模型单硬件，未读到原始指标表格；0.10 release notes 原文未取到（web_extract 被拦）
- 关联 [[#推理控制平面与共享 KV 溯源（2026-10-01 归档）]]、[[MLSys2026论文精选]]

## TP/PP/混合并行的解析权衡模型（arXiv 2610.05305，2026-10-24 归档）
整理自 [[inbox/2026-10-21-大模型推理-TP-PP-HB并行权衡解析模型.md]]
- 把分布式推理端到端延迟分解为「计算 + GPU 间通信 + 流水线气泡」三部分，分别推出 TP 集合通信、PP 点对点通信与流水线利用率的闭式表达 → 「TP 还是 PP」从经验之争变成按硬件/模型/负载可算的判据
- 主要结论：PP 适合算力密集的 prefill（每层同步开销被摊薄），TP 适合延迟敏感的 decode（无 fill/drain 气泡损失）；混合 PP4TP2 / PP2TP4 落在两者之间，用 TP 的通信开销换掉部分 PP 气泡
- 验证方式：固定 8 GPU，比较 TP8 / PP8 / PP2TP4 / PP4TP2 四种配置与模型预测是否吻合
- 读法：与本库 [[MLX生态2026]] 的 Mac 实测（2 机 pipeline 的收益在 prefill、4 机 tensor 才拉 decode）是同一权衡的硬件侧证据；与 [[MLSys2026论文精选]] 的 Revisiting Pipeline Parallelism（动态 chunk + delay scheduling 让 PP 反超 TP）互补——一个给调度手段，一个给选型判据

## 信通院词元服务报告 + GOSIM KVCDN 工作坊：观测口径与 KV 资产化（2026-10-24 归档）
整理自 [[inbox/2026-10-24-大模型推理-信通院词元服务性能监测报告]]、[[inbox/2026-10-24-大模型推理-GOSIM深圳KVCDN工作坊议题]]
- 信通院《大模型词元服务性能监测报告（2026 年）》（10/23，人工智能研究所等三家）：2026 年 6 月国内词元日均消耗约 500 万亿，是 2025 年 12 月的近 4 倍；发布方口径为 2026 年国内大模型均价翻倍、每秒吞吐量也翻倍
- 监测维度：调用成功率、吞吐速度、首词时延、稳定性、价格、上下文窗口，数据覆盖 2026 年 1–8 月，对比原厂官方平台与第三方平台上的 DeepSeek / Qwen / GLM / Kimi → 等于给 MaaS 建立公共可比价基准；具体排名与分数在报告全文，本条只记总量与维度
- GOSIM Shenzhen 2026（10/16–17）KVCDN 专场工作坊：议题把 KV Cache 从单机显存问题抬到分布式资产层面——异构 PD → 跨区域 AI 基建、LMCache 生产级多进程 KV Cache、SGLang 中的 KV 用法、UCM 面向智能体规模推理的生命周期管理、FlexKV 池化实践、Turbo 用网络内聚合服务长上下文
- 读法：主线从「单机 KV 管理」上移到「跨区域池化 + 生命周期」，LMCache 讲的是可观测性与容错而非又一组命中率 benchmark；与 [[#llm-d 0.10 生产化收口 + agentic MTP 并发前沿（2026-10-20 归档）]] 的「KV 放置与搬运成为一级设计对象」同线

## vLLM v0.32：低精度 KV cache 铺开、解析器下沉到 checkpoint（2026-10-30 归档）
整理自 [[inbox/2026-10-27-大模型推理-vLLM-v032低精度KV]]
- v0.32.0 含 814 个 commit、363 位贡献者（121 位新人）；节奏约两周一个 release（v0.30.0 9-22 → v0.31.0 10-05 → v0.32.0 10-18）
- NVFP4 KV cache 扩到 SM8x/SM12x，并支持 SM90、SM12x 上的 XQA decode 与 SM100 上 MiniMax-M3 稀疏注意力；GLM-5.x 默认走 FP8 KV cache → **低精度 KV 从「可选优化」变成默认路径，量化粒度下沉到 KV 层**
- 新增 `POST /v1/systemone` 端点，对结构化状态回答 choice/noul/score，默认开启，已适配 Cloudflare/clef-omni、LiquidAI/d1-3B、DiffusionGemma
- 新增 `hf` 工具/推理解析器（`--tool-call-parser hf`、`--reasoning-parser hf`），直接读 checkpoint 自带的 `response_template`，模型不再需要专属 parser；Python 与 Rust 前端同步
- 承接 v0.30 的 Fast Start（per-GPU 权重缓存守护进程 + `--load-format ipc_cache`，CUDA IPC 映射）→ 冷启动时间已是一等指标
- 口径：取自 release notes 摘要，未见逐项 benchmark

## TPU 上的原生 PyTorch 推理引擎：vLLM-TorchTPU 与 SGLang-TorchTPU（2026-10-30 归档）
整理自 [[inbox/2026-10-27-大模型推理-TPU原生PyTorch引擎vLLM-SGLang]]
- Google 在 TorchTPU 之上新建 vLLM / SGLang 两个引擎后端，分别与 Inferact、RadixArk 合作；思路是「平台插件」：保留各自 worker 模型、调度器、KV cache 管理与 API server，只把算子与层实现换成 Pallas 自定义 kernel，模型定义基本复用上游 PyTorch
- KV 搬运统一走开源 C++ 库 TPU Sync：直接读 PyTorch tensor 背后的 device buffer，**无需 host 端 staging copy**，独立线程在 Python 之外跑；vLLM 经 KV connector 接入，SGLang 当作 PD 分离的传输后端与 HiCache 的 I/O 后端
- 能力面覆盖 agentic 全栈：自动前缀缓存（含 hybrid 模型）、KV 分层卸载到 host DRAM / Managed Lustre、MTP/EAGLE-3/DFlash/DSpark/n-gram 投机解码、context parallel、FP8 KV、FP8/FP4(MXFP4·NVFP4)/INT8/INT4
- 基准走 SemiAnalysis InferenceX + AgentX，宣称 Ironwood（TPU 7x）每百万 token 成本 2.01 美分；已是 preview 客户生产在用
- 读法：厂商正把「引擎」变成可插拔平台层，护城河从调度算法转向 KV 搬运与 kernel 库；与 [[#TP/PP/混合并行的解析权衡模型（arXiv 2610.05305，2026-10-24 归档）]] 互补
- 口径：官方博客自报数字，未做第三方复核



