# Hexagon-MLIR：高通 NPU 的开源 AI 编译器栈
- 来源: https://arxiv.org/abs/2602.19762
- 日期: 2026-07-19
- 标签: #AI编译器 #MLIR #Triton #高通 #NPU

## 摘要
高通发布 Hexagon-MLIR 开源编译器栈，支持将 Triton 内核和 PyTorch 模型直接编译到 Hexagon NPU 上运行，打通了从高层 DSL 到边缘 AI 硬件的全链路。

## 要点
- 基于 MLIR 构建，定义 Hexagon 专用 Dialect，复用上游 MLIR 生态
- 支持 Triton Language 作为前端，降低 NPU 编程门槛
- 提供 PyTorch 集成路径，直接编译 torch 模型到 NPU
- 2026 年 2 月开源发布，面向移动端/边缘侧 AI 推理
