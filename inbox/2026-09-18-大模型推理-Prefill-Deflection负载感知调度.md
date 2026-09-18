# 负载感知的 Prefill 偏转：让 decode 节点替 prefill 干活（arXiv 2607.02043）
- 来源: https://arxiv.org/abs/2607.02043
- 日期: 2026-07-02
- 标签: #大模型推理 #PD分离 #调度 #TTFT

## 摘要
PD 分离把 prefill 与 decode 放在不同 GPU 池以消除相互干扰，却制造了新的不对称：突发重尾负载下 prefill 节点打满、decode 节点算力闲置。在 2P2D 的 A100 生产式集群上，P95 TTFT 中真正的 prefill 执行只占 2–23%，其余全是排队与跨节点 KV 传输。

## 要点
- 方案：主动偏转（deflection）——让 decode 节点把排队请求的 prefill 以 chunked-prefill 步骤插进自己"在飞"的 decode 批里执行
- 决策：对每个排队请求估算它在 prefill 节点与各 decode 节点上的 TTFT，并在 decode 节点上搜索「不破坏 in-flight decode 的 TBT SLO」的最大 chunk 计划，只在 decode 路径确实改善尾延迟时才偏转
- 收益主源：被偏转请求的 prefill 就地运行于 decode 节点，跨节点 GPU-GPU KV cache 传输被直接消除
- 实测（vLLM 实现 + DeepSeek-V2-Lite + 生产式 trace）：P95 TTFT 最高降 81%，SLO 达成率最高 +79%，路由开销亚毫秒级（<1ms/请求）
- 意义：「分离」不是硬约束，按瞬时负载在两类节点间动态搬运 prefill 比静态配比更能守住尾延迟 SLO
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[#KV Cache 作为 Agent 运行时：交互性其实是推理引擎问题（Yandex Research）]]
