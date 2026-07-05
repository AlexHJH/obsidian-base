# SGLang vs vLLM：Qwen3.5 推理性能差距拉大到 5 倍
- 来源: https://github.com/vllm-project/vllm/issues/36215
- 日期: 2026-07-05
- 标签: #推理框架 #vLLM #SGLang #投机解码 #性能优化

## 摘要
社区实测 Qwen3.5-35B-A3B 模型在相同硬件上，SGLang 推理速度比 vLLM 快 3.1~5.3 倍。两者均已公司化（Inferact 估值 8 亿美元，RadixArk 估值 4 亿美元），推理框架进入系统化竞争阶段。投机解码（Speculative Decoding）成为 2026 年推理加速的核心技术方向。

## 要点
- SGLang 的 RadixAttention 和前端 DSL 协同设计是其性能优势关键
- vLLM 通过量化+投机解码组合优化追赶，FOSDEM 2026 有专题演讲
- Google Research 发布投机解码回顾，确认该技术在不损失质量前提下可提速 2-3 倍
- BentoML 推出 llm-optimizer 工具，支持跨框架（vLLM/SGLang）统一基准测试
