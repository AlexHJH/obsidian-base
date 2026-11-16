# harness 决定 token 成本，模型只决定准确率天花板

- 来源: https://arxiv.org/html/2607.22585v1
- 日期: 2026-07-30
- 标签: #AI-Agent #agent-harness #评测方法 #成本

## 摘要
把「脚手架」当作隐藏变量做受控实验：同一模型换 harness，解出同一道题的 token 消耗最多差 40 倍，而 pass@1 差异仅 0–8 个百分点。结论是 harness 主要影响成本与失败指纹，模型能力才影响成功率上限——所以「换框架提升 5 个点」多半是噪声。

## 要点
- Terminal-Bench 2.0 官方数据：Claude Opus 4.5 在两个 harness 上 52.1% vs 57.8%，输入 token 256.9M vs 3.9M（65×）——5.7 分的差距付了 65 倍的钱。
- 实验面：Terminal-Bench Pro 分层 50 题子集，跨模型配对比较，95% bootstrap CI 除最大差距外均含 0。
- 失败指纹可跨模型复现：Goose 恒为 REASON 循环、OpenHands-SDK 为 VERIFY/MAX_TURNS、OpenCode 为空转超时 → 是 harness 的结构性偏差，不是模型性格。
- 对 LongLoop Console 的直接含义：验收契约该冻结在 harness 层，但要同时记账 token/步数，否则「更可靠」可能是拿 40× 预算换来的。参见 [[AI-Agent-2026格局]]。
