# Hexagon-MLIR: 高通开源 AI 编译器栈
- 来源: https://arxiv.org/abs/2602.19762
- 日期: 2026-06-14
- 标签: #AI编译器 #MLIR #Triton #高通 #NPU

## 摘要
Qualcomm 发布 Hexagon-MLIR，基于 MLIR 的开源 AI 编译栈，可直接编译 Triton kernel 和 PyTorch 模型到 Hexagon NPU 上运行，打通了「写 Triton → 跑在手机 NPU」的路径。

## 要点
- 基于 MLIR 构建，复用上游生态基础设施
- 支持 Triton 语言 → Hexagon NPU 的端到端编译
- 结构化编译流程：图优化 → 算子级 lowering → 代码生成
- 开源，降低移动端 AI 模型部署的门槛
