# VeriHarness：用生成方自己的模型当验证器，把「一致地漏掉需求」挖出来
- 来源: https://arxiv.org/abs/2610.00972
- 日期: 2026-10（Google Cloud AI Research；代码 github.com/google-research/veriharness）
- 标签: #AIAgent #Verification #LongHorizon #假完成 #LongLoop

## 摘要
Google 的 VeriHarness 不训 verifier、也不要参考答案或评分规则：给生成方同一个模型一个 workspace、取证工具与可复用验证技能，让它对 N 条 rollout 做两件事——**disagreement resolver**（rollout 互不一致时，拿环境证据裁决谁对）与 **consensus challenger**（所有 rollout 都一致时，反倒去查有没有集体漏掉的需求），再据此选或重建最终产物。

## 要点
- 五个 long-horizon workspace benchmark + 两个前沿模型（Gemini 3.5 Flash / Claude Opus 4.8，各自既生成又验证），selection score 全部最高
- 证据驱动的 revision 相对单条 rollout 平均 +6.2 / +6.4 分；验证技能能从失败反馈里自演化，且迁移到现成 agent CLI 后大部分收益保留
- 方法论关键：**一致 ≠ 正确**。多数 voting 只压随机误差，压不掉「同一模型、同一盲区」的系统性遗漏，所以必须有专门角色去打共识
- 对 [[LongLoop Console]] 的直接映射：验收契约检查器应是「有 workspace + 取证工具 + 可演化技能」的 agent，而不是一次性 LLM judge；「防虚假完成」= consensus challenger + 逐条需求对环境回读
- 口径提醒：生成器与验证器同源，自评偏置没被拆开；作者开源约 2.6 万条 rollout（自称成本 >$10 万）供复现
- 关联 [[AI-Agent-2026格局]]
