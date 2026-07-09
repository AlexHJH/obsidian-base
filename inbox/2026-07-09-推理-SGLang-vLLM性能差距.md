# SGLang vs vLLM：2026 年中性能差距拉大，推理引擎系统化竞争
- 来源: https://www.spheron.network/blog/vllm-vs-tensorrt-llm-vs-sglang-benchmarks
- 日期: 2026-07-09
- 标签: #推理引擎 #vLLM #SGLang #LLM推理 #benchmark

## 摘要
2026 年中多个独立基准测试显示 SGLang 在吞吐和延迟上持续领先 vLLM。Fish Audio 实测 Qwen3.5-35B-A3B 上 SGLang 快 3.1-5.3×；H100 平台 SGLang p99 TTFT 仅 13.1ms（vLLM 23.6ms）。推理引擎从开源项目进入系统化商业竞争。

## 要点
- SGLang RadixAttention 在 prefix-heavy 场景（对话/RAG）吞吐优势显著
- vLLM V1 重构后多硬件兼容性仍领先，社区规模约 3× SGLang
- TensorRT-LLM 在 Blackwell H100 上 gpt-oss-120b 达 5.8× 吞吐（投机解码加持）
- Inferact(vLLM) 估值 $800M vs RadixArk(SGLang) $400M，推理引擎成 AI 基础设施主战场
- 趋势：从「选引擎」到「多引擎控制平面」——GPUStack 等统一编排层兴起
