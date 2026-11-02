# LLVM 官方文档里有 SpacemiT 的 IME 子集，没有 Zvt
- 来源: https://llvm.org/docs/RISCVUsage.html
- 日期: 2026-11-02
- 标签: #LLVM #RISC-V #IME #编译器

## 摘要
LLVM 的 RISC-V 使用指南在厂商扩展清单里已收录 `XSMTVDot` / `XSMTVDotII`——SpacemiT 的 IME 实现子集，指令前缀 `smt.`，声明为 SpacemiT Vector Dot Product Extension **v1.0.0**，且明确写着"是完整 IME 规范的严格子集，以正确反映 X60/A100 硬件能力"。全文检索无 `Zvt`、无 `Zvopmm`：标准矩阵扩展至今没有上游工具链入口。

## 要点
- 两条 SpacemiT 条目：`XSMTVDot` 对应 **X60**，`XSMTVDotII` 对应 **A100**；都只实现"hardware-adapted subset"而非全规范
- 重要措辞：子集不等于 IME，LLVM 明确把版本号绑在 SpacemiT 自家 spec（v1.0.0）上，而非绑 Zvvm 的 v0.9
- 同页还有 Andes 一整套 `XAndesVDot` 5.0.0 等（`nds.` 前缀）→ 厂商私有矩阵/点积扩展 + 自家 toolchain 是当前常态，标准缺位下生态自然分裂
- 后果：任何写矩阵代码的人现在只能在"厂商 intrinsic（不可移植）"与"无指令可用"之间选；IME/VME 一旦 ratified，上游需要重新走一遍 clang intrinsic + SelectionDAG/GlobalISel + 调度模型，这段空窗是真实的工程债
- 对 Triton→RISC-V 的含义：Triton 后端目前无法把 `tl.dot` 降到标准矩阵指令；短期只能落到 `smt.` 系列或手写 lowering，中期要盯 LLVM 对 Zvvm/Zvt 的 RFC 而非等发布
- 待核：Binutils 2.47 / LLVM 23 中 SpacemiT 基座支持的具体 commit 面（二手源称 7 月落地，未读 release note）
