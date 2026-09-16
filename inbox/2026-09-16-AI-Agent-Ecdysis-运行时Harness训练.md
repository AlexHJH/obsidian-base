# Ecdysis：区分「模型缺陷」与「Harness 缺陷」的运行时 Harness 训练
- 来源: https://arxiv.org/abs/2609.11677
- 日期: 2026-09-10
- 标签: #AIAgent #Harness #自我演化 #失败归因

## 摘要
自我演化 harness 的现有做法是「反复执行 + 改代码搜索」，既慢又容易过拟合到观测到的失败。Ecdysis 指出真正的瓶颈是缺少有原则的失败归因：一次失败可能反映模型自身缺陷，也可能是 harness 系统性缺陷，盯着单次失败优化会做出无谓的模型特化迁就。它用跨样本失败聚合把适配偏向系统性缺陷。

## 要点
- 批级跨实例失败聚合：联合分析多个任务实例的失败证据，找重复出现的跨任务模式
- Failure-Driven Collaborative Refinement：多角色诊断失败原因，迭代精修 harness 修改规格（spec）而非直接改代码
- 结果：harness 训练加速至多 1.84×，产出 harness 的推理准确率 +18.56%
- 定位：与 Meta-Harness（端到端优化 harness）、Self-Harness 同属「harness 可自动优化」路线，差异在强调归因粒度
- 关联 [[AI-Agent-2026格局]]、[[MLSys2026论文精选]]
