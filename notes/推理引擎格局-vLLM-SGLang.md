# 推理引擎格局：vLLM vs SGLang

- 整理自: [[inbox/2026-06-15-vLLM-SGLang推理引擎格局]], [[inbox/2026-06-17-vLLM-SGLang商业化-推理引擎]]
 整理自: [[inbox/2026-06-15-vLLM-SGLang推理引擎格局]], [[inbox/2026-06-17-vLLM-SGLang商业化-推理引擎]], [[inbox/2026-06-23-推理分离架构18月回顾-DisaggInference]]
- 标签: #推理引擎 #vLLM #SGLang #开源 #大模型部署

## 商业化（2026）
两引擎相继公司化，推理引擎成 AI 基础设施主战场：
- **Inferact (vLLM)**：a16z 领投 $150M 种子轮
- **RadixArk (SGLang)**：Accel 领投，$400M 估值
- DeepSeek 推理引擎基于 vLLM 早期分支魔改，将核心优化回馈社区（优先并入 vLLM/SGLang）
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
Prefill（计算密集，打满 Tensor Core）与 Decode（访存密集，吃满 HBM 带宽）硬件需求相反，混跑同一 GPU 互相 stall。分离架构将两者拆到不同实例/集群，可获约 2 倍吞吐提升。
- **vLLM 实现**：双实例（prefiller + decoder），用 connector 传输 KV cache
- **Prefill-as-a-Service**：1 个 prefiller 服务多 decoder，跨集群进一步优化
- **核心挑战**：KV cache 跨节点传输开销；2026 年 NIXL（NVIDIA）+ UCX/GDS 成标准后端
- **落地**：Meta、Perplexity 已生产部署；vLLM 仍 experimental，SGLang 跟进
- 整理自 [[inbox/2026-06-18-推理分离-Prefill-Decode-Disaggregation]]
补充整理自 [[inbox/2026-06-23-推理分离架构18月回顾-DisaggInference]]（2026-06-26 归档，UCSD Hao AI Lab 18 个月回顾）
- DistServe 提出 18 个月后，几乎所有生产级框架（NVIDIA Dynamo、vLLM、SGLang、LMCache、Mooncake、llm-d）均已采用分离架构
- **NIXL**（NVIDIA Inference Transfer Library）成 2026 KV cache 跨 GPU 传输标准，支持 RDMA 线速传输
- **NVIDIA Dynamo**：分离推理编排框架（路由 + KV 传输 + 自动扩缩容）
- **AMPD**（arXiv 2602.14516）：面向多轮对话的分离推理，解决增量 prefill 调度

## 关联
- Apple Silicon 本地推理见 [[MLX生态2026]]
- 算力扩张与先进封装见 [[AI芯片推理硬件2026全景]]

## KV Cache 瓶颈与 TurboQuant 4-bit 量化
整理自 [[inbox/2026-06-21-大模型推理优化全景-KVCache与TurboQuant]]
- 竞争核心从算力转向显存利用率/吞吐/单位 Token 成本/延迟四维博弈
- **KV Cache 显存瓶颈**：30B 模型 128k 上下文单条即占 32GB（FP16），超 RTX 4090 物理显存上限
- **TurboQuant**：99% 普通通道压至 4-bit、1% 离群通道保 FP16，显存降 75%、精度损 <1.2%
- **连续批处理**：GPU 利用率 45% → 85%+，尾延迟降 60%
- 组合优化（量化 + vLLM + 稀疏化 + Serverless）：百万 Token 成本 $100 → $5，降本 95%

## 推理系统治理：多引擎控制平面（2026-06-25 归档）
整理自 [[inbox/2026-06-25-推理-推理系统治理与多引擎共存]]
- 推理层从「模型附属组件」升级为 AI Infra 核心层；推理成本已成 AI 商业化决定项
- 模型侧（MoE/多模态/Agent/超长上下文）+ 硬件侧（GPU/NPU/定制加速器）双线推高复杂度 → 多引擎并存成工程必然
- 控制平面（如 GPUStack）把推理从「项目级资产」升级为「平台级资产」
- 关联 [[AI-Agent-2026格局]]（Agent 长程任务推高推理复杂度）

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
