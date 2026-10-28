# IME（Zvvm）工具链承诺只有 LLVM，GCC 明确不做
- 来源: https://riscv.atlassian.net/wiki/spaces/IMEX/pages/598867969/IME+Ratification+Plan
- 日期: 2026-09-30
- 标签: #RISC-V #IME #Zvvm #编译器 #LLVM

## 摘要
IME 批准计划把验证路径压在 LLVM 上：只做汇编 + IME intrinsics 原型（OpenChip 的 Muhammad Kamran / David del Rio），GCC 一栏直接写 N/A。同时 9-30 邮件列表启动了一轮 IME internal review，说明它仍在评审周期内、v0.9.0 ARC candidate 尚未转正式。

## 要点
- 工具链边界：LLVM 侧 assembly + intrinsics，**不做自动向量化调优**；GCC 无原型 → 上游 GCC 支持短期是空白
- 计划表把 "Specification Ratified (v1.0)" 列在 08-27，但 release 仍标 pre-release/not ratified → 计划日期不可当事实引用
- 顺序约束：Zvdot4a8i（点积）被安排在**任何**矩阵扩展（IME/VME/AME）之前 ratified
- OS 责任差异：VME 复用现有向量状态、无新寄存器文件；IME 扩向量寄存器堆并加结果寄存器 → 上下文切换要改（Ubuntu 工程博客口径）
- 关联 [[RISC-V矩阵扩展两年进展-2024-2026]]、[[AI编译器2026格局]]
