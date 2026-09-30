# SOSP 2026 会中：AgenticOS + 新增 HARNESS 工作坊，agent 运行时被单独立题

- 来源: https://sigops.org/s/conferences/sosp/2026/workshops.html , https://os-for-agent.github.io , https://binds.ch/blog/three-papers-sosp-workshops-2026 , https://sigops.org/s/conferences/sosp/2026/schedule.html
- 日期: 2026-09-30（工作坊于 9/29 举行，主会 9/30–10/2，布拉格 Clarion Congress Hotel）
- 标签: #SOSP2026 #AgentOS #隔离 #验证 #MLSys

## 摘要
SOSP 2026 的 9/29 工作坊日把「agent 作为一等 OS 负载」摆上台面：AgenticOS 2026（第 2 届）与 PACMI 之外，还新增了 HARNESS 工作坊（Hardening Agent Runtimes: Networking, Execution, State, Security）。论文群集中在隔离、形式化表示、不可逆操作的风险记账与「外部化屏障」——即 agent 验收与效果核查问题终于在系统会议有了落点。

## 要点
- AgenticOS 论文清单里有几条正对「防虚假完成 / 越界执行」：`Externalization Barriers: An OS Abstraction for Untrusted Agent Exploration`（MPI-SWS + 多伦多）、`The Irreversibility Budget: Fleet-Level Risk Accounting and Admission Control for Agent OS`（把不可逆动作当成要预算的资源）、`Agent libOS: A Runtime Substrate for Capability-Controlled Self-Evolving LLM Agents`（清华）
- 阿里云的 `The Abstractions Are Slipping: Four OS Mismatches in the Agent Era` 与 Wisconsin 的 `Isolation in the Age of Agents` 指向同一议题：现有隔离/权限抽象与 agent 实际负载错配
- 新增 HARNESS 工作坊的四维（网络 / 执行 / 状态 / 安全）把「运行时加固」拆成可分工的工程问题，与 AgenticOS 的原语/调度议题互补
- 主会同日 Session 3D（GPU kernels and accelerators）：Morphable Kernels（清华）、Ave（HKUST，数据流不变量守护 agent 改 kernel）——与 [[MLSys2026论文精选]] 既有条目衔接
- 与本机 LongLoop 的对照：不可逆预算 / 准入控制、外部化屏障 = 验收契约冻结与验证检疫的学术版本；评审仍缺「通过率要配上怎么过关的」这类口径
- 关联 [[MLSys2026论文精选]]、[[AI-Agent-2026格局]]
