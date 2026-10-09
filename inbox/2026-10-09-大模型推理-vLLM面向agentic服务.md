# vLLM × AgentX：面向 agentic 负载的 KV 缓存优化
- 来源: https://vllm.ai/blog/2026-09-08-vllm-agentx
- 日期: 2026-09-08
- 标签: #大模型推理 #vLLM #KV-Cache #Agent #Mooncake

## 摘要
vLLM 与 AgentX 合作，针对 agentic 负载（海量前缀复用、多轮、工具调用）优化服务：把 **Mooncake Store** 集成成分布式 KV cache 池，使 prefix 缓存能超出单机 GPU 显存并跨每个 engine 复用；KV cache offloading 被确立为 first-class 能力，同时补齐新模型架构支持。

## 要点
- 兼容性：新增稀疏注意力、压缩注意力、线性注意力架构支持的同时，异步调度、P/D 分离、投机解码、并行等既有特性均不退化
- 新架构细节：Kimi K3 用 MLA + Kimi Delta Attention（KDA），KV 被压到单一 latent 空间；纯张量并行（TP）会把该 latent cache 在各 rank 复制一份 → 效率低，需新的并行策略
- 趋势：KV cache 从「单机显存管理」升级为「跨 engine / 跨层的分布式缓存系统」，与 Mooncake、MemServe 同一条线
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[AI-Agent-2026格局]]
