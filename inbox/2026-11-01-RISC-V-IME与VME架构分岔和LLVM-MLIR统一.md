# IME/VME 架构分岔与 LLVM-MLIR 统一 lowering
- 来源: https://riscv-europe.org/summit/2026/presentations
- 日期: 2026-06-11
- 标签: #RISC-V #IME #VME #LLVM #MLIR #编译栈

## 摘要
Summit Europe 2026（博洛尼亚）上 Philipp Tomsich 复盘矩阵扩展拆成四路一年后的进展：IME 与 VME 已双双逼近 freeze，并开始用一条公共 LLVM-MLIR lowering 同时覆盖两者，避免软件栈随硬件分裂。这是目前为止最清楚的"两条路线为何共存"的官方说法。

## 要点
- Zvvm（IME）的 tile 几何不是硬编码尺寸，而是由 VLEN、SEW 与新增 aspect-ratio 字段 λ 代数推导 → VLEN=256 编译出的二进制在 VLEN=65536 上直接跑，无需重编译
- 同一套 partial-VL 机制同时覆盖嵌入式"一次一列"的流式与大块 HPC tiling；microscaling 通过 vm-bit 集成进同一编码
- VME 走相反方向：vector 寄存器作输入、专用 tile 寄存器作累加器 → 单指令计算强度更高，代价是新增架构状态与软件迁移
- 生态关键动作是把 IME/VME 收敛到同一条 LLVM-MLIR lowering，让 AI/ML 框架只对一个抽象编程；但"有 lowering 计划"≠"已进上游"，落地状态仍按 [[2026-10-30-RISC-V矩阵扩展-LLVM只认厂商子集]] 的 release 口径核
- 对 Triton→RISC-V：λ 驱动的几何推导天然适配 tile 参数化，可作为后端 tile-size 选择的理论依据；VME 的独立累加器则要做双路径 lowering
