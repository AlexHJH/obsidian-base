# VME 进入 BoD 表决前一周：时间线走完，规范与 Spike 还没对齐
- 来源: https://riscv.atlassian.net/wiki/spaces/VMEX/pages/663617995/Ratification+Plan ；https://lists.riscv.org/g/tech-vme/topic/clarification_on_zvt_tile/121802356
- 日期: 2026-11-05
- 标签: #RISC-V #VME #Zvt #规范进程

## 摘要
VME（Zvt 家族）按 TG 批准的里程碑计划已走完 Public Review（10-09 起），TSC Ratification Approval 定在 11-04，BoD Ratification Approval 定在 11-18；但同期列表上仍在讨论 Zvt v0.4 与 Spike 参考实现对 tile punning 别名化的字节偏移公式不一致。

## 要点
- 里程碑链：Plan Approval 07-16 → Internal Review 07-30 → ARC Freeze Request 08-20 → Public Review 10-09 → TSC 11-04 → BoD 11-18
- 争议点：`Clarification on Zvt Tile Punning Aliasing formulas` 线程对比 Zvt v0.4（commit 31a76665）与 Spike（commit 5a760667），两边算元素在 tile 状态线性化后的起始字节偏移结果不同
- 含义：punning（同一 tile 状态按不同 TEW/元素类型重解释）是可移植性的关键语义，规范与参考实现分歧意味着首批商用实现可能各自"照自己那份"落地
- 待核：11-04 TSC 表决是否通过、11-18 BoD 前是否会插入 spec 修订；列表正文未能抓取，仅见主题与摘要
- 判读：Process 上的 "ratified" 不等于语义已收敛，验收要盯 Spike/P-扩展模型与规范 adoc 的 diff

关联：[[RISC-V矩阵扩展两年进展-2024-2026]]
