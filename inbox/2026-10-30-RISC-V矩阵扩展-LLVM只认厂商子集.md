# 上游 LLVM 只实现"厂商硬件适配子集"，标准 IME 仍无编译器对标
- 来源: https://llvm.org/docs/RISCVUsage.html
- 日期: 2026-10-30
- 标签: #RISC-V #IME #AME #LLVM

## 摘要
LLVM 官方 RISC-V 文档列出两个与矩阵扩展相关的前缀：`XSMTVDot`（SpacemiT X60，`smt.` 前缀，实现的是 SpacemiT Vector Dot Product Extension v1.0.0，文档明确写"全 IME 的严格子集"）与 `XSMTVDotII`（对应 A100）。也就是说上游代码生成的目前是厂商 Dot-Product 子集，不是 IME `Zvvm` 全量语义；AME 侧邮件列表最新公开物仍是 `-Draft-20260615`，尚未进入正式阶段。

## 要点
- 状态分层要分清：IME `Zvvm` 家族（`Zvvmm/Zvvfmm/Zvvmtls/Zvvmttls`）停在 v0.9 Freeze（2026-08-10，pre-release、未 ratified）；AME 仍是 weekly meeting 上的 draft PDF → "有 release 标签"≠"已批准"
- LLVM 的 `XSMTVDot` 是"硬件适配子集"，v1.0.0 版本号属于 SpacemiT 自己的 dot-product spec，不能当 IME 标准落地信号引用
- 结果含义：想跑标准 `Zvvm` 语义（如 `Zvvmttls` 的 tile 级 load/store）目前没有上游后端，工具链事实上被绑定到单一厂商前缀
- 对 Triton→RISC-V 后端的启示：优先对齐 `smt.` 子集可立即拿到 LLVM 路径，但 tile/TMA 类抽象需自带 lowering；IME 标准语义要等 GCC/LLVM 正式支持
- 关联 [[RISC-V矩阵扩展两年进展-2024-2026]]、[[AI编译器2026格局]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
