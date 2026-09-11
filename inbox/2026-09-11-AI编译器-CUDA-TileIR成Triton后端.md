# CUDA Tile IR 成为 Triton 后端：NVIDIA 官方打通「tile 语义」全链路
- 来源: https://developer.nvidia.com/blog/advancing-gpu-programming-with-the-cuda-tile-ir-backend-for-openai-triton
- 日期: 2026-09-11
- 标签: #AI编译器 #Triton #CUDA-Tile #MLIR

## 摘要
NVIDIA 发布 Triton-to-TileIR 后端（2026-01-30），让 Triton 内核不再下沉到 PTX，而是直接编译到 CUDA Tile IR（CUDA 13.1 引入的 tile 级 MLIR 表示），完整保留 tile 语义到后端。对 Alex 关注的 GP+CC 分离 SoC 而言，这是「上层 tile DSL → 硬件自有 tile IR」路径的官方范本。

## 要点
- CUDA Tile 把 SIMT 线程模型换成 tile 抽象：开发者只写 block 级操作，线程调度/硬件映射/资源分配交给编译器与运行时
- 切换方式：环境变量改编译管线，或按 kernel 粒度选后端，已有 Triton 代码无需重写
- 现状：需从源码构建、CUDA 13.1+、Blackwell；限制是 tensor-of-pointer 模式性能欠佳（建议改 TMA load/store）
- 信号：tile 级 IR 成为 NVIDIA 官方「自有方言」层，Triton 从替代 CUDA 变成其前端之一
- 关联: [[AI编译器2026格局]]（参见其中 CUDA Tile IR 方言条目）[[AI芯片推理硬件2026全景]]
