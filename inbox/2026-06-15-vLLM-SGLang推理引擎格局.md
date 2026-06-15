# vLLM vs SGLang：开源推理引擎的社区演进与格局
- 来源: https://www.inclusion-ai.org/blog/llm-landscape-vllm-sgl
- 日期: 2025-12-17
- 标签: #推理引擎 #vLLM #SGLang #开源 #大模型部署

## 摘要
Ant Group 开源办公室深度剖析 vLLM 和 SGLang 两大推理引擎的社区起源、技术演进与生态竞争。两者均源于 UC Berkeley Ion Stoica 实验室，核心创新分别为 PagedAttention 和 RadixAttention（复用 KV cache），如今已趋同支持 Continuous Batching、Chunked Prefill、Speculative Decoding 等主流技术。2025 年起两者从「数字军备竞赛」转向可复现的端到端评测。

## 要点
- vLLM (2023.6) 借 OS 分页思想实现 PagedAttention，吞吐提升最高 30×，社区 ~2000 PR 贡献者
- SGLang (2024.1) 以 RadixAttention 扩展 KV cache 跨请求复用，社区规模为 vLLM 一半但增速快
- vLLM 核心贡献者来自 Red Hat，SGLang 来自 xAI/Oracle/LinkedIn；约 30% SGLang 贡献者也给 vLLM 交过代码
- 2025 年初 vLLM V1 重构解决 CPU 调度开销瓶颈，DeepSeek V3/R1 发布推动两者二次爆发
- 其他值得关注：TensorRT-LLM（NVIDIA 硬件深度优化）、Llama.cpp（边缘推理）、LMDeploy（双后端）
