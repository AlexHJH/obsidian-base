# Apple Core AI 发布：端侧推理三梯队成型
- 来源: https://developer.apple.com/videos/play/wwdc2026/326 (WWDC26)
- 日期: 2026-07-05
- 标签: #MLX #Apple-Silicon #CoreAI #端侧推理 #WWDC2026

## 摘要
WWDC 2026 发布 Core AI 框架，定位为 CoreML 的神经网络继任者，支持 3B–70B 参数模型在 iPhone/iPad/Mac/Vision Pro 全平台端侧运行。Apple 正式确立三梯队：Core ML（经典 ML）→ Core AI（Transformer）→ MLX（研究/自定义权重）。

## 要点
- Core AI 提供 Swift 统一 API，覆盖 CPU/GPU/Neural Engine，支持 Qwen/Mistral/SAM3 等主流模型
- 内置 AOT 模型编译 + Xcode 性能分析，首发加载延迟大幅降低
- MLX 获 WWDC 三场专场（分布式推理/本地 Agent/Swift 数值计算），生态地位升级
- 关联 [[MLX生态2026]]
