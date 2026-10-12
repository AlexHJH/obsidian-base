# AgenticOS@SOSP 2026 keynote：agent 需要的是 POSIX，而不是内核
- 来源: https://os-for-agent.github.io
- 日期: 2026-09-29
- 标签: #MLSys顶会 #SOSP #Agent运行时 #Harness

## 摘要
第 2 届 AgenticOS（与 SOSP 2026 同地，9/29 布拉格）keynote 由 MPI-SWS 的 Laurent Bindschaedler 给出：《The Harness Wars: Why Agents Need a POSIX Before a Kernel》。论点是缺的不是内核，而是「模型与世界之间那个运行时」的契约接口。

## 要点
- 三个失败样本：下单超时重试导致重复发货却记成成功；压缩上下文时静默丢掉一条常驻规则后直接发版；人审通过后 rebase 改了被审代码、流程重试却复用旧批准
- 归因：三例里模型决策都合理、结果都错——OS 从不判断「程序行为是否合理」，只做隔离；但 agent 自己生成动作，判断权于是落到 harness / agent OS 身上
- 会议侧信号：best paper 给了 #18「并发 GPU-coding agent 负载下保持 GPU profiling 准确性」与 #27 AgentProf「AI agent 的语义剖析」——验证与可观测被摆到工作坊 C 位
- 关联 [[MLSys2026论文精选]]、[[AI-Agent-2026格局]]、[[LongLoop Console]]
