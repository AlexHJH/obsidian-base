# VME（Zvopmm）已列为 ratified 2026-11，成为首个落地的 RISC-V 矩阵扩展
- 来源: https://riscv.github.io/adm-spec-dashboard?filter=bod ；https://neuling.org/riscv-software-news/riscv-software-news-2026-11.html ；https://lf-riscv.atlassian.net/wiki/spaces/VMEX/pages/663617995/Ratification+Plan
- 日期: 2026-11-22
- 标签: #RISC-V #矩阵扩展 #VME #IME #ISA标准

## 摘要
官方 spec dashboard 的 VME 行现在标成「Ratified in 2026 11」，11 月软件月报同时把 VME 从跟踪管线里移除（原状态 Freeze），与 TG 批准计划里 11-18 的 BoD 日期对得上。三条路线里 VME 先落地，IME 仍停在 v0.9.0 ARC 候选。

## 要点
- 这是一次方向性更正：11-19 归档判断「VME/IME 一起后移到 27Q1、11-18 BoD 没发生」，同一看板两周后给出 ratified 2026-11 → 看板是整体刷新，不能当逐条事件流读
- VME 规格名 Zvopmm；技术形态不变：两条向量寄存器做外积、结果累加进独立矩阵累加器（N² 次 MAC），RVV 依赖只要 Zve32x
- IME 仍在 v0.9.0（2026-08-24, commit `5a2d0f6`，release 明写 pre-release / not ratified），版本阶梯 0.9 Freeze → 0.99 Ratification-Ready → 1.0 Ratified，后两档的 tag 都还没出现
- 工具链仍是短板：LLVM 只有 SpacemiT `smt.`/`XSMTVDot(II)` 的硬件适配子集，标准 Zvvm 全量语义无上游后端；VME 侧完整栈仍靠 Google Coral 的下游 patch
- 口径：看板与月报都是搜索快照（web_extract 今日被拦，未读 latest.csv 原文），批准结论须用 riscv-vme 的 v1.0 release tag 复核；批准 ≠ 编译器/OS 可用

关联 [[RISC-V矩阵扩展两年进展-2024-2026]]、[[AI编译器2026格局]]
