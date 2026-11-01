# VME 进入批准窗口：TSC 11/4 表决、BoD 11/18，而 IME 还卡在 0.9 Freeze
- 来源: https://riscv.atlassian.net/wiki/spaces/VMEX/pages/663617995/Ratification+Plan
- 日期: 2026-11-01
- 标签: #RISC-V #VME #IME #Zvt #标准化

## 摘要
VME TG 的批准计划已排到具体日期：公开评审 2026-10-09 起，TSC 批准表决 11-04，BoD 批准 11-18——即三天后 VME 可能成为三种矩阵扩展里第一个 ratified 的。同期 IME 的 GitHub release 仍停在 v0.9 Freeze（2026-08-04），ARC 评审与 GC 表决未完成，计划表上的 8-27 ratified 已过期。

## 要点
- VME 里程碑链：Plan 7/16 → 内部评审 7/30 → ARC freeze 请求 8/20 → (9/25) → 公开评审 10/9 → TSC 11/4 → BoD 11/18，均为计划/by 日期
- VME 语义：两条 vector 寄存器做外积、累加进专用 tile 累加器，一条指令完成 N² 次 MAC；新增架构状态是 tile 寄存器，与 RVV 紧耦合，可从 MCU 线性放大到 HPC
- 与 IME 分野：IME（`Zvvm`）把 tile state 全部放进标准 V 寄存器堆、无新增状态；VME 用独立累加寄存器换更高计算强度——两条路线是"软件集成成本 vs 计算强度"的取舍
- IME 口径：v0.9.0 Freeze 覆盖 `Zvvmm/Zvvfmm/Zvvmtls/Zvvmttls`，release note 明写 Freeze 阶段还剩 ARC Review 与 Governing Committee Approval 两项未完成 → 只能称 freeze，不能称 ratified
- 二手源陷阱：检索到"IME/VME 本周在博洛尼亚达成 spec freeze"的说法，实际 Summit Europe 是 6/8-12 且 IME 至今未 ratified；引用前必须对齐 release tag
