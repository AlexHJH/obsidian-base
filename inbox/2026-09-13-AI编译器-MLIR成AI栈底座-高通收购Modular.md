# MLIR 成 AI 编译栈底座：高通 39 亿美元收购 Modular
- 来源: https://www.thesoftwarefrontier.com/p/exploring-how-mlir-works-the-compiler
- 日期: 2026-09-13
- 标签: #AI编译器 #MLIR #Triton #半导体

## 摘要
2026 年三件独立事件指向同一层基础设施：高通 6/24 宣布以约 39 亿美元（全股票，人均约 2600 万美元）收购 Chris Lattner 的 Modular；特斯拉 FSD v14.3 用 MLIR 从零重写编译器与运行时、声称反应时间改善 20%；NVIDIA CUTLASS 4 的 CuTe DSL 也经 MLIR JIT 后再交给 ptxas。MLIR 不是「AI 版 LLVM」，而是构建编译器的机器，已成为 AI 软硬件栈的事实协调点。

## 要点
- 生态侧同步铺开：AMD MLIR-AIE 1.2（Ryzen AI NPU 底层）、高通开源 Hexagon-MLIR（吃 PyTorch 图与 Triton kernel 落到 Hexagon NPU）、华为 Ascend HIVM 方言栈均以 MLIR 为交汇点
- LLVM IR 的窄契约（标量 SSA + 平坦内存模型）在降到 IR 后丢掉张量形状/循环结构/融合机会 → tensor 编译器需要多级抽象，这正是 MLIR dialect 体系存在的理由
- MLIR 新增 Python 绑定可直接用嵌入式 DSL 定义 dialect/op 与 rewrite，编译器不再在回路里，方言原型迭代周期大幅缩短
- 战略含义：谁控制 dialect 定义权与 lowering 路径，谁就握住了硬件差异化最窄的通道；编译器能力正被芯片厂商内化（Lattner 团队整体并入高通）
- 关联 [[AI编译器2026格局]]、[[AI芯片推理硬件2026全景]]
