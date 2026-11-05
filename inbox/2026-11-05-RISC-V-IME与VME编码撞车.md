# IME 的 Zvvmtls 与 VME 的 Zvt 撞编码：两套矩阵标准难共存于一个 hart
- 来源: https://lists.riscv.org/g/tech-vme ；https://github.com/riscv/integrated-matrix-extension/releases
- 日期: 2026-11-05
- 标签: #RISC-V #IME #VME #指令编码

## 摘要
tech-vme 列表出现《Potential encoding collision between Zvvmtls vmtl.v/vmts.v and Zvt tile load/store instructions》（4 条消息），指 IME 的 tile 装载/存储指令与 VME 的 Zvt tile load/store 存在编码冲突；这是两条并行矩阵路线在 ISA 层面的直接摩擦。

## 要点
- 冲突对象：IME 侧 `Zvvmtls` 的 `vmtl.v` / `vmts.v`，对 VME 侧 Zvt 的 tile load/store
- 数量级判断：IME 走"复用 vector 寄存器 + 少量 CSR"，VME 走独立矩阵累加器（outer product 累进入大矩阵），两者都要新增 tile 搬运指令，编码空间必然重叠
- 对厂商的含义（推断，待核）：同一 hart 想同时兼容 IME 与 VME 需要重编码或二选一；对已按 IME 子集出货的核（如 SpacemiT X60/A100 的 `smt.` / XSMTVDotII 子集）是路线锁定风险
- 版本坐标：IME 已到 v0.9 Freeze（08-10，tag v0.9-freeze-20260804）与 v0.9.0 ARC Review Candidate（08-24，commit 5a2d0f6），0.99 才是 ratification-ready
- 待核：冲突是否已收敛为"命名空间互斥"或需正式 spec 修订；正文未抓取

关联：[[RISC-V矩阵扩展两年进展-2024-2026]]、[[AI编译器2026格局]]
