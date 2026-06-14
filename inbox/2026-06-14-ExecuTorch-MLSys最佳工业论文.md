# ExecuTorch 获 MLSys 2026 最佳工业论文
- 来源: https://pytorch.org/executorch
- 日期: 2026-06-14
- 标签: #MLSys #端侧推理 #PyTorch #ExecuTorch

## 摘要
ExecuTorch 是 PyTorch 官方端侧推理框架，获 MLSys 2026 Best Industry Paper。支持从手机到嵌入式设备的广泛部署，搭配 Arm SME2 可实现 3.9× 端到端加速。

## 要点
- 统一框架覆盖手机、MCU、嵌入式等异构设备
- 通过 XNNPACK delegate + SME2 优化移动端推理
- 支持量化 (INT8/FP16) 和算子级性能剖析
- 已成为 PyTorch 端侧部署的标准方案
