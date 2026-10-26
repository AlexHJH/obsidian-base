# AgentRewind：把「回到过去」做成 agent 运行时能力
- 来源: https://arxiv.org/abs/2608.14380
- 日期: 2026-08-14
- 标签: #AI-Agent #长任务 #运行时 #可恢复执行

## 摘要
长视界 agent 的早期错误会同时污染上下文与环境状态，后续动作难以逆转。AgentRewind 是运行时恢复框架：对 agent 上下文与受控环境打「对齐的检查点」，出错时回到早先状态、带着前次尝试的信息继续执行。作者同时提出 MettleBench——由一串相互关联的需求构成的工程类长任务基准，同时度量任务完成与部分进度。

## 要点
- 关键在对齐：上下文快照与环境快照必须同一时刻，否则回滚后 agent 记忆与环境事实不一致
- 与既有路线分工：plan refinement / safety check 是「事中防错」，AgentRewind 补的是「错后恢复」这一空白
- 跨任务、多模型、多执行策略、多 harness 上，成功率和平均 checklist 进度都优于基线 → 恢复能力与 harness 无关，可做成运行时层
- 基准设计值得抄：用 checklist 度量部分进度，而不是 pass/fail —— 与 LHTB「79% 未完成 run 仍在大步推进」同一结论
- 对 [[LongLoop Console]] 的直接含义：checkpoint 需环境+上下文成对、带版本，回滚后可审计；「恢复」应是一等状态转换而非重跑
- 口径：摘要取自 arXiv abs 页原文；MettleBench 规模、加速幅度与对照基线细节未读全文
