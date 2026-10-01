# HORIZON：长任务退化何时发生、坏在哪里（arXiv 2604.11978）

- 来源: https://arxiv.org/abs/2604.11978 ; https://xwang2775.github.io/horizon-leaderboard ; https://arize.com/blog/long-horizon-agent-benchmarks-field-guide
- 日期: 2026-04-13
- 标签: #AIAgent #长任务 #基准 #失败分类 #诊断

## 摘要
《The Long-Horizon Task Mirage?》提出 HORIZON：用 depth/breadth 扩展把任务内在视界 H 拉长，测量性能随 H 的退化曲线，并用 7 类失败分类在 4 个领域做机制归因。它同时回答「在哪一档视界开始崩」与「崩的具体机制是什么」，是一个可系统构造任务初始诊断基准。

## 要点
- 任务构造硬约束：baseline(s=1) 须可被最优策略解、每步不可被最优策略跳过、breadth 扩展须可加、oracle 解法可编程验证 → 否则退化曲线是噪声
- 关键提问换位：不是「Agent 会不会失败」，而是「在哪个内在视界 H 上断崖」——把「长」变成可测的自变量
- 7 类失败分类跨 4 个领域做机制归因 → 输出「坏在哪」而不只是「多少分」
- 横向对照：长任务基准正在碎片化，每个都在「真实性 vs 可验证性」之间交易，并在某个接缝处漏分（Arize 场记，2026-06）
- 判定：没有程序化 oracle 与不可跳过步的基准，长任务分数不可比
- 关联 [[AI-Agent-2026格局]]、[[MLSys2026论文精选]]
