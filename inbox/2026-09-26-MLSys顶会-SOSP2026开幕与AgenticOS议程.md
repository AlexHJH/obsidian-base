# SOSP 2026 工作坊侧：AgenticOS 把「agent 的 OS 层」单独立题
- 来源: https://os-for-agent.github.io , https://binds.ch/blog/three-papers-sosp-workshops-2026 , https://sigops.org/s/conferences/sosp/2026
- 日期: 2026-09-29
- 标签: #MLSys顶会 #SOSP #AIAgent #OS

## 摘要
SOSP 2026（9/29–10/2，布拉格，会期与奖项状态见 [[inbox/2026-09-24-MLSys顶会-顶会日历NeurIPS放榜与SOSP开幕]]）同期同城挂了三个工作坊：HotStorage '26（9/28–29）、PACMI 2026 与 AgenticOS 2026。其中第二届 AgenticOS（Workshop on OS Design for AI Agents，9/29 下午）明确征集 agent 负载的 OS 级机制。

## 要点
- AgenticOS 征稿范围＝agent 系统的原语、隔离模型、调度技术与可观测性，收 position paper 与经验报告，目标是为 agent 系统定义专属操作系统抽象
- Bindschaedler 的 keynote 主张：OS 在这里有活干——agent 需要一个**限制它能做什么、同时不拖慢它该做什么**的底座；随会公开两篇 PDF《The Irreversibility Budget》《Don't Trust the Code, Check Its Effects》
- 同作者组三篇：AgenticOS 两篇（分别与 Jinhao Hu / Ashvin Goel、与 Bardia Mohammadi，后者主题 Externalization Barriers）+ PACMI 一篇（Practical Adoption Challenges of ML for Systems，同日同场地）
- 与论文侧分工对照：[[MLSys2026论文精选]] 收录的 SOSP 录用主线是 LLM 训练/推理基础设施与确定性，工作坊侧则把「agent 作为一等 OS 负载」独立出来——验收契约、效果核查这类问题开始有系统会议落点
- 待观察：workshop 论文预计会期放出，9/29 后可回补原文核对结论
- 关联 [[MLSys2026论文精选]]、[[AI-Agent-2026格局]]
