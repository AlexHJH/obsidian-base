# AgenticOS 2026 议程公开：预算与能力被做成 OS 原语

- 来源: https://os-for-agent.github.io ; https://binds.ch/blog/three-papers-sosp-workshops-2026
- 日期: 2026-09-29（会期），10 月补程序页
- 标签: #MLSys #SOSP #AgenticOS #隔离 #预算

## 摘要
第 2 届 AgenticOS（与 SOSP 2026 同址，9/29 布拉格 Congress Hotel）程序公开，议题集中在 agent 的 OS 原语、隔离模型、调度与可观测性。Bindschaedler 的 keynote 主张「OS 在这里有活干」——agent 需要一个限制它能做什么、同时不拖慢它该做什么的底座。

## 要点
- 论文（标题+机构取自程序页）：**Patient Bytes: A Reliability-Budgeted Scheduler for Agentic LLM Workflows**（UCSC/HKUST/阿里）；**Agate: Capability Microkernels as a Natural Substrate for AI Agents**（Huawei 2012 Labs）；**Securing Agentic AI with OS-Level Intent-Driven Capabilities**（INESC-ID/里斯本大学/Oracle Labs）
- 随会公开三篇 PDF：《The Irreversibility Budget》（不可逆动作当预算资源）、《Externalization Barriers》（内核决定不可信 agent 的探索产物何时离开沙箱）、《Don't Trust the Code, Check Its Effects》
- CfP 议题清单：compiler–OS 协同设计（agent 感知的自适应 JIT）、agent 负载的 GPU/加速器虚拟化、agent 调起工具/代码/数据流的隔离、agents managing systems（内核调优/异常检测/恢复）、agent 间通信原语、agent 可靠性
- 与 SOSP 主会呼应：M2K 显式化 model-kernel 接口做验证、Ave 用数据流不变量当编译期护栏——同一句「不指望 agent 守规矩，而是让约束可被机器独立判定」
- 对本库主线的意义：不可逆预算 + 准入控制 + 效果核查 = [[LongLoop Console]] 验收契约冻结与验证检疫的学术版本
- 口径提示：程序页与 PDF 经搜索摘要读取（web_extract 被拦），作者与标题可能有个别拼写差异，引用前核对原页
- 关联 [[MLSys2026论文精选]]、[[AI-Agent-2026格局]]
