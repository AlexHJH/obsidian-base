# Stream2LLM：流式输入重叠预填，降低首 token 延迟
- 来源: https://rajveerbachkaniwala.com/blog/2026/05/19/stream2llm-overlap-context-streaming-prefill-reduced-ttft
- 日期: 2026-06-28
- 标签: #大模型推理 #MLSys2026 #TTFT #vLLM #流式预填

## 摘要
MLSys 2026 论文，扩展 vLLM 支持并发下的流式输入，将上下文流式传输与预填重叠，首 token 延迟（TTFT）较非流式基线降低 3.9–11×。

## 要点
- 核心问题：长上下文预填阻塞 decode，拖高 TTFT
- 两阶段调度器：阶段1按优先级选请求 + 可行性计算，阶段2分配资源，失败则抢占
- 低负载下各流式调度器性能趋同；高负载下收益最大（最高 11×）
- 关联 [[推理引擎格局-vLLM-SGLang]]（vLLM 生态新优化）
