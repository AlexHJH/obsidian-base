# AgentRewind：给长任务 agent 一个「真的能回滚」的 undo

- 来源: https://arxiv.org/abs/2608.14380 （HTML 版 https://arxiv.org/html/2608.14380v1）
- 日期: 2026-08-14
- 标签: #AIAgent #长任务 #checkpoint #可恢复性 #Harness

## 摘要
长任务里早期错误会同时污染 agent 的上下文与环境状态，后续动作很难反悔；现有方法只做计划修正与安全检查，出错之后没有补救手段。AgentRewind 在每次 LLM 决策边界把「上下文 + 环境」成对记成 checkpoint，agent 判断走不下去时自己挑一个更早的点回滚重放，而不是从头重来。

## 要点
- 关键数字（mini-SWE-agent + GPT-5.4，82 道长任务工程题）：Continue 62.2% → Restart-with-experience 78.0% → AgentRewind 87.8%；平均 checklist 进度 81.4% → 94.3%
- 弱模型收益更大：GPT-5.4 mini 从 33.7%（Continue）升到 51.2%
- 两侧缺一不可：只回滚上下文降到 65.9%、只回滚环境崩到 43.9% → 环境被污染是纯推理补不回来的
- 反直觉对照：Safety Review 34.1%，比直接继续还差；「多一道检查」不是免费的
- 步数不等于进度：GPT-5.4 平均轨迹最短（50.5 事件）却成功率第二高，GLM-5.1 等模型 300+ 事件反而更低
- 对 LongLoop 的含义：回滚单元必须是「上下文 + 工作区 + 外部副作用」三者一致的 checkpoint；agent 自称完成仍只是待审信号，验收交外部 verifier

关联：[[AI-Agent-2026格局]]
