# 编译器锚定的分层诊断：LLM 自动优化 Triton Kernel（Ascend NPU）
- 来源: https://arxiv.org/abs/2607.23089
- 日期: 2026-08-12
- 标签: #AI编译器 #Triton #LLM #NPU

## 摘要
arXiv 新论文（2607.23089，2026-07）把 LLM kernel 优化建模为跨层诊断问题：仅靠编译反馈与 profile 指标只能知道 kernel 慢、却不知后端编译器为何没实现优化。系统在模式检查、profile 诊断、IR 归因、编译器源码分析间逐级升级，最后提出有证据支撑的源码改写。

## 要点
- 实现于 Triton + 昇腾 950 NPU，NPUKernelBench 派生基准共 37 个 kernel
- 几何平均加速 4.35×，中位数 2.73×；22/37 超 2×、13/37 超 5×
- 呼应 MLSys 2026 kernel 生成竞赛趋势：编译器证据链是 LLM 优化 agent 的关键能力

相关: [[AI编译器2026格局]] [[MLSys2026论文精选]] [[AI芯片推理硬件2026全景]]
