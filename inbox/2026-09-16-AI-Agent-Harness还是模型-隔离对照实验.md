# Harness 还是模型？用防污染私有套件隔离 Harness 效应
- 来源: https://arxiv.org/abs/2609.11987
- 日期: 2026-09-08
- 标签: #AIAgent #Harness #Benchmark #评测方法

## 摘要
「厂商原生 harness 配自家模型能解更多任务」是个普遍假设。作者用私有、防污染控制的 256 题套件（仓库任务 + 截止日之后的竞赛题）做同模型配对对照，结论是平均优势并不存在：Opus 4.8 上 claude-agent-sdk 比 deepagents 低 1.25pp，GPT-5.5 上 codex SDK 高 1.25pp，95% 置信区间都横跨 0。

## 要点
- Opus 上两个分层方向相反：仓库类任务原生 harness 落后 9.0pp，竞赛类任务领先 23.7pp（label-permutation p=0.003）——但分层是看过数据后选的，需设计性复现
- 成本同样不占优：中性 harness 每解一题贵 1.3–1.6×（Opus 4.8）、1.2×（GPT-5.5）；因 Anthropic 账号 58 次运行无 usage 记录，计费排序仍未定论
- 81 次撞时间墙的运行里有 22 次其实已产出通过补丁 → 完成率与正确率必须分开度量
- 该版修正了 8 月稿件中自家埋点的 usage 语义缺陷，属罕见的数据自曝修正
- 关联 [[AI-Agent-2026格局]]
