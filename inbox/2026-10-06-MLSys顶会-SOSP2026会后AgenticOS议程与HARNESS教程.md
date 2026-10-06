# SOSP 2026 会后回补：AgenticOS 议程全公开，HARNESS 实为半天 tutorial
- 来源: https://os-for-agent.github.io ; https://binds.ch/blog/harness-sosp-2026 ; https://binds.ch/blog/three-papers-sosp-workshops-2026
- 日期: 2026-09-29（工作坊当日）；议程/材料随会期后放出
- 标签: #MLSys顶会 #SOSP #AI-Agent #AgenticOS #运行时加固

## 摘要
第二届 AgenticOS（SOSP 2026 同期，9/29 布拉格）完整议程已上线：除此前记录的隔离/风险三篇外，新出现具身 agent 运行时与「agent 上下文陈旧」两个具体议题，且验证侧首次成组投稿。另一处口径修正——SOSP 官方名单里的 HARNESS 实为 Bindschaedler + Akhil Arora 主讲的半天 tutorial，而非平行 workshop。

## 要点
- 具身侧：**TypeGo: An OS Runtime for Embodied Agents**（Yale，Lin Zhong 组）把机器人/具身 agent 当成 OS 一等负载；**When Agent Context Goes Stale: Incoherence in Volatile Agent Context**（港大）专治上下文被压缩/回喂后不一致——正是本机语音栈「历史怎么压缩」的学术对应
- 验证侧成组：**Formal Methods as the Harness for AIOS**（中科院软件所，闪电报告）、**An AgentOS Needs a Formal Representation of Agents**、**LLM Agent Capabilities Should Follow Task Intent and Context Source**（授权收敛到 intent 与来源）——验收契约这一脉开始有人做 formal harness
- 议程结构：Opening 含 Best Paper Awards；Keynote 为 Bindschaedler（MPI-SWS，题目当日未定）；Isolation and Risks 场次含 UW-Madison《Isolation in the Age of Agents》与清华 Agent libOS；阿里云《The Abstractions Are Slipping》同场
- 材料口径：Bindschaedler 博客公开三篇 PDF 摘要（The Irreversibility Budget、Externalization Barriers、Don't Trust the Code, Check Its Effects）——比 10-03 记录多一篇，可据此回补
- HARNESS（Hardening Agent Runtimes: Networking/Execution/State/Security）为半天 tutorial，主题是长存活、重工具 agent 的运行时加固与落地经验，与 AgenticOS 的理论议程互补
- 关联 [[MLSys2026论文精选]]、[[AI-Agent-2026格局]]
