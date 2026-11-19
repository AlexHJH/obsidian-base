# VME 明天过 BoD 即 1.0，IME 还卡在 ARC：RISC-V 矩阵扩展分叉成两条时间线
- 来源: https://lf-riscv.atlassian.net/wiki/spaces/VMEX/pages/663617995/Ratification+Plan
- 日期: 2026-11-17
- 标签: #RISC-V #矩阵扩展 #IME #VME #ISA标准

## 摘要
RISC-V 矩阵扩展的两条官方路线进度彻底分开：VME（Vector Matrix Extension，Zvt 家族）按 VMEX TG 批准计划走完 freeze（9-25）、公开评审（10-9）、TSC 批准（11-4），11-18 提交 BoD 批准即达 1.0；而 IME（Zvvm 家族，SpacemiT 主导，仅用现有 RVV 向量寄存器）仍停在 v0.9 freeze，GitHub 上 Ratification-Ready（v0.99）无 tag，发布说明明确「Freeze 阶段收尾工作（治理委员会批准票 + 有记录的架构评审）尚未完成」。

## 要点
- VME 的设计取舍：与 RVV 解耦又耦合——源操作数从向量寄存器读、周边 AI/ML 计算复用 RVV，但新增 accumulator 寄存器堆与配置寄存器；矩阵维度无关（类 vsetvl/vl），可伸缩到高吞吐到低面积各档
- IME 的约束条件正是另一头：不新增矩阵 state，全部矩阵 tile 放在 v0-v31，性能上限被向量寄存器容量锁住——Google Coral 文档已直说这点，并称 Zvt v1.0 正式版「late 2026」
- 实际后果：没有已批准标准就没有可移植矩阵 intrinsics，现在买到的手矩阵加速 RISC-V 核都走厂商私有扩展 + 厂商工具链；11-18 之后 VME 侧才第一次有「可规划的上游」
- 软件栈欠账：IME 计划里 LLVM 只做汇编 + intrinsics（无优化），GCC 明确不做——即令 ratified，编译器侧仍有一段裸奔期
- 口径：时间线取自 VMEX/IMEX 官方 Wiki 与 GitHub release 说明；BoD 批准为计划日期，未见投票结果公告
- **11-19 更正**：官方看板显示两条线都在 2026-09-16 被整体推到 27Q1，VME 的 ARC 评审尚未开始、仓库自 7-29 起无提交；11-18 的 BoD 未发生。见 [[2026-11-19-RISC-V-矩阵扩展双双后移27Q1]]
- 关联 [[RISC-V矩阵扩展两年进展-2024-2026]]、[[notes/sources/2026-10-25-RISC-V矩阵扩展-一手来源与时间线]]
