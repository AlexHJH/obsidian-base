# Qualcomm 开源 Hexagon-MLIR：Triton/PyTorch 直编 NPU
- 来源: https://www.qualcomm.com/developer/blog/2026/02/build-faster-on-hexagon-npu-tritor-pytorch-with-hexagon-mlir-open-source
- 日期: 2026-08-10
- 标签: #AI编译器 #MLIR #Triton #NPU #Qualcomm

## 摘要
Qualcomm 于 2026 年 2 月开源 Hexagon-MLIR——基于 MLIR 的 AI 编译栈，可将 Triton kernel 与 PyTorch 模型直接编译到 Hexagon NPU 执行，配套 arXiv 论文（2602.19762）。核心动机是 GenAI 新算子与融合模式迭代太快、手写算子库跟不上，编译栈提供敏捷路径。

## 要点
- 能力：Triton kernel 编译执行 + PyTorch 模型编译执行，利用多线程与 HVX 向量扩展做性能优化
- 定位：与商用工具链互补的开源实验栈（WIP），面向开发者早期接入
- 意义：延续 triton-shared 思路，将 Triton 生态从 GPU 扩展到 NPU/边缘，MLIR 统一降级路线再添一例

相关: [[AI编译器2026格局]] [[MLX生态2026]]
