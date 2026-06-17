# vLLM vs SGLang: 推理引擎双雄商业化，DeepSeek 开源推波助澜
- 来源: https://particula.tech/blog/sglang-vs-vllm-inference-engine-comparison
- 日期: 2026-06-17
- 标签: #大模型推理 #vLLM #SGLang #DeepSeek #Inferact #RadixArk

## 摘要
2026 年初 vLLM 和 SGLang 相继公司化（Inferact $800M / RadixArk $400M），推理引擎成为 AI 基础设施主战场。SGLang 凭 RadixAttention 在 H100 上吞吐领先 29%，vLLM 胜在生态广度。DeepSeek 将其推理引擎优化回馈社区，优先并入 vLLM/SGLang。

## 要点
- Inferact (vLLM) a16z 领投 $150M 种子轮，RadixArk (SGLang) Accel 领投 $400M 估值
- SGLang 在 prefix-heavy 场景（RAG/多轮对话）吞吐领先 vLLM 高达 6.4x
- vLLM 硬件生态更广（TPU/Trainium/Gaudi），贡献者 3x 于 SGLang
- DeepSeek 推理引擎基于 vLLM 早期分支魔改，将核心优化回馈开源社区
- 选型建议：DeepSeek 模型 + 对话场景 → SGLang；多硬件 + 批处理 → vLLM
