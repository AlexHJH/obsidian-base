# kw-bench：同一个模型，换 harness 就从 0.00 到 0.54
- 来源: https://arxiv.org/abs/2610.36405
- 日期: 2026-10-29
- 标签: #AIAgent #Harness #Benchmark #确定性评分

## 摘要
kw-bench 用真实审计/税务项目构造任务（首个任务族：跨 SAP ECC、S/4HANA 部分迁移和顾问重构三代系统的五年增值税重建），给的是**原始系统导出**（分页页眉、本地数字格式、残缺关联表、无文档），答案唯一且可逐分核对。评分是确定性的，不用 LLM judge——直接针对"生成式 benchmark 好判、调查式工作难判"这个缺口。

## 要点
- 同一模型 DeepSeek v4.1 Flash 跑 2 小时，只换 harness：两个模型无关的独立 harness 得分 OpenCode 0.542、micro-cc 0.528；两个实验室 harness（Claude Code、Codex）**离开自家模型后双双 0.00**
- 失败机理是"惯性"：在长会话里维持大量弱信号同时存活，而不是单点能力不足
- 前沿模型上任务直接塌缩：Opus 5.5 上 micro-cc 0.9986（22 min）、Claude Code 1.000（25 min）→ 原来的"周级项目"几分钟几美元
- 结论句：**前沿以下，harness 决定模型能走多远**；前沿以上，模型自己吃掉了差异
- 方法论值得抄：确定性评分 + 跨 harness 单模型对照 + 原始脏数据入口，正好卡住"看起来完成"的虚假完成
