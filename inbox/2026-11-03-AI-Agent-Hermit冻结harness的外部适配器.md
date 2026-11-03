# Hermit：模型和 harness 都冻结，只训外面那层适配器
- 来源: https://github.com/Infini-AI-Lab/hermit
- 日期: 2026-10-25（v0.1.0）／解读 2026-10-29
- 标签: #AIAgent #HarnessAdapter #持续学习 #Codex #ClaudeCode

## 摘要
Infini-AI-Lab 开源 Hermit（Apache-2.0，`pip install hermit-harness`）：把"指令、skills、memory、tools、甚至整个仓库"当可训练参数放在 agent 外部，底座模型与 harness 一律冻结，用 RL 式循环（Trainer 跑批次 → judge 打分 → Evolver 提新版本）迭代适配器，每个版本落到 git 树里可回滚可审计。

## 要点
- 与 LoRA 的分界线：LoRA 改模型；harness adapter 改的是模型外的一切，所以能跨 Codex / Claude Code / Pi 迁移，也能作为插件导出
- 自带 5 种进化法：GEPA、SkillOpt、MWM、Harness²、Continual Harness；ACE、ReHarness 待发
- 数字（GLM 5.3 Flash 上自报、多为单次跑）：CL-Bench 数据库探索 MWM 0.795 vs in-context 0.580；OfficeQA 上 Harness² 0.541→0.651，代价约 $0.16 与 7 分钟/题
- 对 LongLoop 的直接启发：把"验收契约/防虚假完成"当成 adapter 的一部分去优化，比改框架代码更好版本化
- 待核：单次跑无方差、且 judge 与被测模型同源，收益里有多少是自评偏置没有拆开

关联：[[AI-Agent-2026格局]]
