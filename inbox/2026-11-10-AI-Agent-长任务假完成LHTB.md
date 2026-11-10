# LHTB：长任务 agent 的「假完成」被量化了
- 来源: https://arxiv.org/abs/2607.08964 （项目页 https://zli12321.github.io/LHTB，代码 github.com/zli12321/LHTB）
- 日期: 2026-07-09
- 标签: #AIAgent #长任务 #评测 #防虚假完成 #TerminalBench

## 摘要
腾讯 HY LLM Frontier 的 Long-Horizon-Terminal-Bench 用 46 个容器化终端任务（9 类，从复现论文结果到调试编译器工具链）测 agent 能"撑住"多久：平均 239 个 episode、~89 分钟、120–320 步，题面只给轻量公开检查，重活交给隐藏 verifier 事后按子任务回放打分（dense reward，超时的部分进度也算数）。18 个模型没有一个接近饱和。

## 要点
- 天花板很低：最佳模型在 46 题里 reward=1.0 只解出 7 题，达到 R≥0.95 的不到三分之一（HF 榜含 LHTB 2.0 的 77 题口径，无模型超过 14 题）
- 失败形态不是"走错一步"：79% 未解决 run 是超时那一刻 agent 仍在推进——瓶颈是完成度而非单步正确率
- 假完成（false finish）：124 次自终止里 14 次在 R≥0.75 就退出，agent 自认为做完了而隐藏 verifier 不认，即自验证弱 + 停止判断差
- Harness 层面的关键设计：agent 宣布完成后不停，harness 跑隐藏 verifier，若未通过就把反馈注回同一 session 继续，直到预算耗尽——把"宣布完成"降级为一次状态转换
- 口径提醒：LHTB 1.0（2026-07 快照）早于 binary-feedback 默认项与隔离的 LangChain verifier，新硬化的跑分不得与该快照合并
- 对 LongLoop 的直接含义：验收契约必须由外部 verifier 回放证据，agent 的 done 只是一个待审信号

关联：[[AI-Agent-2026格局]]、[[MLSys2026论文精选]]
