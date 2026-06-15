# TVM vs XLA vs Triton：AI编译器格局深度分析
- 来源: https://www.modular.com/blog/democratizing-ai-compute-part-6-what-about-ai-compilers
- 日期: 2025-03-12
- 标签: #AI编译器 #TVM #Triton #XLA #MLIR #推理优化

## 摘要
Modular 发布深度系列文章，对比了 TVM、XLA 和 Triton 三类 AI 编译器的定位差异：XLA 面向特定框架优化（TensorFlow/JAX），TVM 追求硬件和框架的广泛覆盖，Triton 则走 Python eDSL 路线让开发者手写高性能 kernel。文章指出 AI 编译器正在从"编译优化"转向"协同设计"。

## 要点
- **XLA**：Google 主导，与 JAX/TF 深度绑定，优化路径成熟但扩展性受限
- **TVM**：Apache 开源，支持多硬件后端（CPU/GPU/FPGA/NPU），灵活性最强但上手门槛高
- **Triton**：OpenAI 推出，Python 语法写 GPU kernel，编译到 MLIR 中间表示，在 LLM 推理优化中应用广泛
- 趋势：三种路线在 MLIR 上趋同，未来竞争焦点是跨硬件可移植性和调试体验
