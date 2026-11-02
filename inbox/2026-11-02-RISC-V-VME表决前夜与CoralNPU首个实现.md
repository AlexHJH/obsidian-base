# VME 距 TSC 表决两天，但规范仓库自 7/28 停笔
- 来源: https://riscv.atlassian.net/wiki/spaces/VMEX/pages/663617995/Ratification+Plan
- 日期: 2026-11-02
- 标签: #RISC-V #VME #Zvt #标准化

## 摘要
VME（Vector-Matrix Extension）的批准表按计划走到 TSC 表决：公开评审 10-09 起，**TSC 11-04**、**BoD 11-18**，将是三种矩阵扩展里第一个 ratified。但 `riscv/riscv-vme` 仓库最后一次提交停在 2026-07-28（release tag `riscv-isa-release-5570c7b-2026-07-29`），整个公审窗口没有任何 spec 改动——说明当前是文档态评审，不是内容还在动。

## 要点
- 里程碑链（均为计划/by 日期）：Plan 7/16 → 内部评审 7/30 → ARC freeze 请求 8/20 → (9/25) → 公开评审 10/09 → TSC 11/04 → BoD 11/18
- 邮件列表口径：内部评审已于 **9/16 完成**，TG 的 Stabilization Milestone 投票 **10/05 关闭**（snippet 级证据，未读原文）
- 命名有两套并存：批准表 "Specification Name" 写 `Zvopmm`，实际文档与公告叫 **Zvt Family of Vector Matrix Extensions**；引用时以 Zvt 为准并注明这一不一致
- 唯一已落地实现是 Google **Coral NPU**：按其文档实现 Zvt **v0.4**，并称官方 v1.0 计划 2026 年底——即硬件在按草案做，规范还在 v0.x
- Coral 的实现细节可当微架构参考：16 个 tile 累加器；`mset` 配置 M/K/N；矩阵引擎作为向量引擎的解耦协处理器（命令队列 → 数据编排 → PE 阵列 FMA → 累加器写回），流水内显式处理气泡（`REMVPIPEBUBB`）
- 语义口径：外积形式，操作数取自 v0–v31 的 128-bit 寄存器，**不新增 2D load/store**，矩阵数据按常规行/列主序走既有访存
- 与 IME 分野仍是老问题：IME 把 tile state 塞进标准 V 寄存器堆、无新状态；VME 加独立累加器寄存器换计算强度
- 待核：Zvopmm 与 Zvt 是否同一规范的正式/旧名；公审期无提交是"已冻结"还是仓库迁移
