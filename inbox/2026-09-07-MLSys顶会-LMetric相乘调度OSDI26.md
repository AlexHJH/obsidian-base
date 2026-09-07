# LMetric：LLM 请求调度——相乘可能就够了（OSDI'26）
- 来源: https://arxiv.org/abs/2603.15202
- 日期: 2026-09-07
- 标签: #MLSys #OSDI #LLM推理 #调度

## 摘要
IPADS（陈榕/陈海波组）在 OSDI'26 提出 LMetric：把 KV-cache 感知与负载均衡两个指标简单相乘作为调度分数，请求路由到分数最小的实例，无需超参调优，TTFT 降 92%。

## 要点
- 关键洞察：线性组合两个指标有效，则"相乘"同样有效，且省去超参权重调优
- 一个指标度量 KV-cache 复用（prefill 亲和），另一个度量实例负载均衡
- 延续"Simple is Better"路线，简单可解释调度胜过复杂启发式
- 关联: [[MLSys2026论文精选]] [[推理引擎格局-vLLM-SGLang]]
