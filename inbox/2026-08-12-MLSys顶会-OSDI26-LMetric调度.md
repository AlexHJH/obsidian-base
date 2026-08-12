# OSDI'26: LMetric——乘法调度指标，免调参 LLM 请求调度
- 来源: https://www.usenix.org/conference/osdi26/technical-sessions
- 日期: 2026-08-12
- 标签: #MLSys #OSDI #LLM推理 #调度

## 摘要
SJTU IPADS 与阿里云在 OSDI'26 提出 LMetric：用两个指标的简单乘法（路由后新增 prefill token 数 × 实例当前 batch 大小）作为调度分数，同时兼顾 KV 命中与负载均衡，且无需任何超参调优。

## 要点
- 关键洞察：乘法形式下超参在比较时相互抵消，天然免调参
- 对比 vLLM-v1 与生产调度器：TTFT 降 92%/39%，TPOT 降 24%/51%
- 已部署生产并通过 canary 验证；论文推导了乘法失效的罕见条件并给出事前检测方法

相关: [[推理引擎格局-vLLM-SGLang]] [[MLSys2026论文精选]]
