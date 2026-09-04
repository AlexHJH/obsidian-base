# AlphaPROBE：图上偏置进化的 Alpha 因子挖掘
- 来源: https://arxiv.org/html/2602.11917v1
- 日期: 2026-09-04
- 标签: #量化交易 #因子挖掘 #LLM #A股

## 摘要
AlphaPROBE 把因子挖掘重构为「有向无环图（DAG）上的策略导航」，用原则化检索 + 图上偏置进化让 LLM 自动生成可解释的公式化因子。

## 要点
- 建模为 DAG 导航：历史因子作节点，进化算子在图结构上偏置搜索
- 骨干 LLM 用 DeepSeek V3.1，Qwen3 Embedding 衡量因子语义多样性（SemDiv）
- 以 ICIR 绝对值作质量度量、缓解因子拥挤；对比 R&D-Agent(Q) 等基线
- 关联: [[A股量化策略2026]]
