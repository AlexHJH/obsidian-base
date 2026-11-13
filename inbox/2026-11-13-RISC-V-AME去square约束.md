# IME 冻了，AME 还在改寄存器布局：去 square 约束，规范构建也甩掉了 UDB
- 来源: https://lists.riscv.org/g/tech-attached-matrix-extension/topics ；https://github.com/riscv/riscv-attached-matrix-extension ；https://riscv.atlassian.net/wiki/spaces/AMEX/pages/55083388/Charter
- 日期: 2026-10（对照核对）
- 标签: #RISC-V #AME #矩阵扩展 #寄存器布局

## 摘要
同族的 IME 已冻结到 0.9，AME 却还在动最底层：10-14 周会首次引入当前 spec 文档，邮件列表同时在讨论「Removing the Square Constraint from the AME Register Layout」，即矩阵寄存器不再绑定方形 tile。仓库同期改成「不带 UDB 依赖」的构建（RVG-476），可独立出 PDF。

## 要点
- 定位差是结构性的：IME 复用 RVV 状态、在向量寄存器里拼 tile；AME 新增独立矩阵寄存器堆，charter 明确要求能脱离 V 扩展单独实现
- 精度策略：常规 HPC 用单宽累加（FP64/FP32），窄类型（INT8/FP4-8/FP16/BF16）用加宽累加器——与 GPU tensor core 的「窄输入、宽累加」同构
- 去 square 约束的动机：tile 几何不该由单个 TLEN 决定；Jan-2026 纪要里矩阵寄存器数已定 16，累加器数实现相关（1/2/4），RVA profile 后续可能强制 4
- 进程判读：还在改寄存器布局，说明 AME 离 freeze 有距离，草案条目不能当稳定 ABI；编译器侧只需锁「tile 寄存器 + 矩阵类型 CSR」这层抽象，具体编码等冻结
- 组织面：副主席提名投票 11-09 截止；四方案（dot-product / IME / VME / AME）仍并存，只有 IME、VME 明确在向 freeze 收口

关联：[[RISC-V矩阵扩展两年进展-2024-2026]]
