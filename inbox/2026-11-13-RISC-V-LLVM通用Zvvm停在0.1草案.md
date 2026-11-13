# LLVM 里的通用 Zvvm 还钉在 0.1 草案，规范已 0.9 冻结：真能用的反而是厂商子集
- 来源: https://llvm.org/docs/RISCVUsage.html ；https://github.com/riscv/integrated-matrix-extension/releases ；https://forum.spacemit.com/t/topic/1554
- 日期: 2026-11-13（对照核对）
- 标签: #RISC-V #IME #Zvvm #LLVM #版本错位

## 摘要
LLVM 文档把 `experimental-zvvmm` / `zvvfmm` / `zvvmtls` / `zvvmttls` 明确标为「实现 0.1 draft specification」，而 IME 仓库 08-10 已发 v0.9 Freeze，中间差 8 个版本号。同期的落地走的是厂商独立 feature 名：`XSMTVDot`（X60）、`XSMTVDotII`（A100），指令前缀 `smt.`。通用路径与厂商路径是两条互不覆盖的线。

## 要点
- 逐栏对齐：规范 = 0.9 Freeze（Zvvmm/Zvvfmm/Zvvmtls/Zvvmttls，08-10）；LLVM 通用 = 0.1 draft，且仍是 experimental，必须 `-menable-experimental-extensions` 并在 `-march` 里显式写版本号
- 别把 `experimental-zvvmm` 当「上游已支持 IME」：0.1 草案与 0.9 冻结文本不是同一份内容，冻结后新增的 tile 几何、`LAMBDA` WARL 行为、microscaling 布局都不在里面
- 厂商子集更实在：LLVM 自述 X60 实装的是 SpacemiT Vector Dot Product 1.0.0、严格是完整 IME 的子集；7 月上游季报称基础支持已随 Binutils 2.47 与 LLVM 23 发布，X100/A100 的 CPU target 也进了 LLVM 和 GCC
- 另一条轻量路线跑得更快：无需矩阵状态的 batched dot 已到 0.2 draft（`zvqwbdota8i/16i`、`zvfqwbdota8f`、`zvfwbdota16bf`、`zvfbdota32f`）
- 对 Triton/MLIR 后端的可操作结论：target 抽象不能按「IME 一个 extension」建，要按 (a) 规范版本 (b) 厂商 feature 名 (c) 实装子集的指令清单 三维建表，否则 `zvvmm0p1` 与冻结版会静默走错 lowering
- 口径：版本号来自 LLVM 文档与 IME release 页两处对照；LLVM 主分支是否已跟进 0.9 未验证，列为待查项

关联：[[RISC-V矩阵扩展两年进展-2024-2026]]、[[AI编译器2026格局]]
