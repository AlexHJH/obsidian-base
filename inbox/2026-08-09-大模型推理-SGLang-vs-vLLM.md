# SGLang vs vLLM 2026：前缀缓存决定胜负
- 来源: https://atomic.chat/blog/llm-updates/sglang-vs-vllm
- 日期: 2026-08-04
- 标签: #大模型推理 #SGLang #vLLM #KV-Cache

## 摘要
两者在独立 prompt 场景性能接近（H100/Llama 3.3 70B 吞吐约 +3.8%），但高前缀复用场景（Agent 系统提示、工具定义）SGLang 的 RadixAttention 比 vLLM 页粒度前缀缓存快约 29%（16200 vs 12500 tok/s）。选型取决于工作负载前缀命中率。

## 要点
- SGLang：RadixAttention 基数树前缀缓存 + 缓存感知调度，结构化输出用 xgrammar，xAI Grok 生产部署达数十万卡
- vLLM：PagedAttention + 连续批处理生态最广，新模型首发适配快；近期新增 Model Runner V2 与 EAGLE 3.1 投机解码
- 估算公式：前缀命中率 = 重复前缀 token / 总输入 token
