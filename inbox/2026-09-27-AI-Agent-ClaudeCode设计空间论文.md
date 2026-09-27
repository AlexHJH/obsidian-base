# Dive into Claude Code：把生产 agent harness 的设计空间写成 70 页论文

- 来源: https://arxiv.org/abs/2604.14228 （代码: github.com/VILA-Lab/Dive-into-Claude-Code）
- 日期: 2026-04-14（arXiv:2604.14228，VILA-Lab / MBZUAI）
- 标签: #AIAgent #Harness #ClaudeCode #架构

## 摘要
作者只靠公开的 TypeScript 产物（v2.1.88）反向拆解 Claude Code：核心是一个 while 循环「调模型 → 跑工具 → 重复」，真正复杂的是循环外围——七种权限模式 + ML 分类器的权限系统、五层 compaction 管线、四种扩展机制（MCP / plugins / skills / hooks）、带 worktree 隔离的子 agent 委派、append-oriented 会话存储。论文提出 5 个人类价值 → 13 条设计原则 → 具体实现的链路，并与 OpenClaw 对照。

## 要点
- 5 个动机价值：人的决策权、安全与安保、可靠执行、能力放大、上下文适应性；据此解释「为什么这样实现」而非罗列功能
- 默认拒绝而非默认放行：Anthropic 自测显示用户会批准约 93% 的权限提示，所以 deny-first + 分级分类是唯一可行解
- 对比结论：同一批设计问题在不同部署语境下答案不同——Claude Code 是绑定单仓库会话的 CLI harness（逐动作安全分级、文件系统记忆），OpenClaw 是多渠道常驻控制平面（边界式访问控制、结构化长期记忆 + dreaming/日记/记忆检索）
- 趋势判断：上下文持久性成为新瓶颈，harness 会成为检测「模型在第 100 步开始不听话」并回灌训练的主要工具
- 关联 [[MLSys2026论文精选]] [[推理引擎格局-vLLM-SGLang]]
