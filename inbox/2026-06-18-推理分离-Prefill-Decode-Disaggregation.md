# Prefill-Decode 分离架构：大模型推理吞吐翻倍的新范式
- 来源: https://www.spheron.network/blog/prefill-decode-disaggregation-gpu-cloud
- 日期: 2026-06-18
- 标签: #大模型推理 #部署优化 #vLLM #SGLang #PrefillDecode #NIXL

## 摘要
Prefill（计算密集）与 Decode（访存密集）两阶段硬件需求相反，混跑在同一 GPU 上导致互相拖累。分离架构将两者拆到不同实例/集群，可获约 2 倍吞吐提升。2026 年 NIXL 已成为 vLLM 与 NVIDIA Dynamo 的 KV cache 传输标准机制。

## 要点
- Prefill 计算密集（打满 Tensor Core），Decode 访存密集（吃满 HBM 带宽），混跑互相 stall
- vLLM 实现方式：运行两个实例（prefiller + decoder），用 connector 传输 KV cache
- 跨集群分离进一步优化：Prefill-as-a-Service 模式，1 个 prefiller 服务多 decoder
- 核心挑战：KV cache 跨节点传输开销；2026 年 NIXL（NVIDIA）+ UCX/GDS 后端成标准
- Meta、Perplexity 均已生产部署；vLLM 仍为 experimental，SGLang 亦跟进
