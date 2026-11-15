# IME 还卡在 ARC 评审：v0.9.0 不是标准，block scale 改成「精确指数偏移」
- 来源: https://github.com/riscv/integrated-matrix-extension/releases ；改动 https://github.com/riscv/integrated-matrix-extension/pull/68 ；计划 https://riscv.atlassian.net/wiki/spaces/IMEX/pages/598867969/IME+Ratification+Plan
- 日期: 2026-11-13
- 标签: #RISC-V #IME #Zvvm #矩阵扩展 #块缩放

## 摘要
状态要先分清：IME 的正式里程碑只到 **v0.9 Freeze**（标签 `integrated-matrix/v0.9-freeze-20260804`，8/10 发布），8/24 发布的 v0.9.0 是提交给 ARC 的评审候选，release note 自己写明「pre-release draft, has not been ratified」。Ratification Plan 原计划 8/17 过 TSC（v0.99）、8/27 完成 v1.0，但仓库 11 月仍在按 ARC 反馈改（11/07 修 SAIL widening index，11/13 合入块缩放语义），tags 里至今没有 v0.99 或 v1.0——实际延期约两个半月。

## 要点
- 11/13 合入的 PR #68「Apply E8M0 block scales exactly」：删掉「把每个 block scale 解码并转换为 C 累加器格式」的要求，改为把合成后的 E8M0 指数**精确**作用于内部部分和，再执行 `rnd` 指定的舍入
- 语义后果：缩放不再引入独立的舍入点，也不产生自己的异常标志；SAIL 模型同步更新（合并前 ptomsich 已在逐条抓「decode」这类措辞）
- 硬件含义：block scale 从「一次数值转换」退化成「一次指数偏移」，实现可以把它并进累加通路，省掉一级舍入与标志判定——对 MX 类块缩放格式的落地成本是实质降低
- ARC 候选相比 freeze 快照还改了：λ 的合法性/选择/WARL 行为、active 与 physical tile geometry 的区分、tile load/store 合法性与 restart、microscaling 的 block-size/容量/算术语义、C intrinsic 与 m16 pair/unpair 语义
- 路线图口径：Tomsich 在 RISC-V Summit Europe 2026（Bologna，6/8–11，Wed 6/10 14:30 特邀报告）称 IME 与 VME 两条线正在「converge on specification freeze」，并已开始用共同的 LLVM-MLIR lowering 路径统一两者——编译器侧不想为两套矩阵扩展维护两条路径
- 厂商子集 ≠ 标准：LLVM 的 RISC-V 后端已实现 `XSMTVDot`（X60）与 `XSMTVDotII`（A100），指令前缀 `smt.`，明确是 SpacemiT Vector Dot Product Extension 1.0.0 的硬件适配**子集**，不是完整 IME

## 口径提醒
「v0.9.0」是 ARC 评审候选的版本号，不是 0.99 的 Ratification-Ready；Freeze 阶段 0.9 的官方定义是「substantially complete + ARC review complete」，仍有实质改动空间（11 月的两次提交即为证）。
