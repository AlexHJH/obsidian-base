# 推理分离架构 18 个月回顾：从 DistServe 到生产标配
- 来源: https://haoailab.com/blogs/distserve-retro
- 日期: 2026-06-23
- 标签: #大模型推理 #PD分离 #DistServe #NVIDIA-Dynamo #NIXL #vLLM #SGLang

## 摘要
UCSD Hao AI Lab 回顾 DistServe（Prefill-Decode 分离推理）提出 18 个月后的产业落地。如今几乎所有生产级推理框架（NVIDIA Dynamo、vLLM、SGLang、LMCache、Mooncake、llm-d）都已采用分离架构，核心思路是将计算密集的 prefill 与内存密集的 decode 分配到不同 GPU 池独立扩展。

## 要点
- Prefill 是计算密集型，Decode 是内存带宽密集型，混部导致两者互相拖慢
- 分离后 TTFT 和 TPOT 可独立优化，吞吐提升约 2×
- **NIXL**（NVIDIA Inference Transfer Library）成为 2026 KV Cache 跨 GPU 传输标准，支持 RDMA 线速传输
- **NVIDIA Dynamo** 提供分离推理编排框架：路由 + KV 传输 + 自动扩缩容
- **AMPD**（arXiv 2602.14516）：面向多轮对话的分离推理，解决增量 prefill 调度问题
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[AI芯片推理硬件2026全景]]
