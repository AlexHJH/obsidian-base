# HIVM：华为 Ascend NPU 的开源 MLIR 方言栈（EuroLLVM 2026）
- 来源: https://llvm.org/devmtg/2026-04
- 日期: 2026-09-13
- 标签: #AI编译器 #MLIR #Ascend #Triton #LLVM

## 摘要
EuroLLVM 2026（4/14–15，都柏林）上华为介绍 HIVM：一族开源 MLIR 方言，把 PyTorch/Inductor → Triton → MLIR(HIVM) → LLVM IR 打通，用 Ascend 专属优化补齐 DaVinci 核心与新代 SIMD+SIMT 执行模型带来的编译挑战，最终落到自研 BiSheng LLVM 后端。

## 要点
- 关键优化面：layout 分配与传播、vector intrinsic 选择/合法化、显式 DMA/transfer 调度与同步插入——与自定义加速器的「编程模型鸿沟」问题同构
- 复用 Triton 作为前端，说明 Triton 已默认为「编译器核心小、后端可扩展」的跨硬件入口
- 同场另一看点：MLIR 在 Python 中定义 dialect/op 与 pass/rewrite 的教程，和 Lean+Sail 验证过的 RISC-V 指令选择（小型可信基座替代 CompCert 式人工证明）
- 关联 [[AI编译器2026格局]]、[[#Triton for MTIA：把 tile 编程模型下沉到定制加速器]]
