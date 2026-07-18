# 投机解码生产落地：vLLM 与 SGLang 方案对比
- 来源: https://consciousengines.com/blog/putting-it-to-work-serving-speculative-decoding-with-vllm-and-sglang
- 日期: 2026-07-18
- 标签: #推理 #SpeculativeDecoding #vLLM #SGLang

## 摘要
投机解码（Speculative Decoding）已从实验室走向生产：用廉价drafter生成多个候选token，目标模型一次forward验证，无损加速20-50%。vLLM支持EAGLE/MTP/draft model等多种方法，SGLang凭RadixAttention在长上下文/多轮场景中命中率更高。

## 要点
- vLLM 支持 EAGLE、MTP、draft model、ngram 等多种投机方法，EAGLE/MTP 收益最高
- SGLang RadixAttention 显著提升前缀缓存命中率，Agent 场景下投机 + 缓存双重收益
- 低QPS内存瓶颈场景收益最大，高QPS算力瓶颈场景收益递减
- llama.cpp 也支持投机解码，本地部署可用小draft模型加速
