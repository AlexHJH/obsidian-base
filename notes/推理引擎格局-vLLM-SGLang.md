# 推理引擎格局：vLLM vs SGLang

- 整理自: [[inbox/2026-06-15-vLLM-SGLang推理引擎格局]]
- 标签: #推理引擎 #vLLM #SGLang #开源 #大模型部署

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

## 关联
- 架构演进见 [[inbox/2026-06-18-推理分离-Prefill-Decode-Disaggregation]]（prefill/decode 分离范式）
- Apple Silicon 本地推理见 [[MLX生态2026]]
