# DeepSeek 开源昇腾基础设施组件：TileLang/DeepGEMM/DeepEP 全量对齐

- 来源: https://www.163.com/dy/article/L834QGAO0511AQHO.html ; https://news.ifeng.com/c/8wpz82KATHw ; https://finance.biggo.com.tw/news/f8a26b65-2476-4f12-b04a-0312be51c16b
- 日期: 2026-09-30
- 标签: #AI芯片 #昇腾 #DeepSeek #AI编译器 #国产算力

## 摘要
DeepSeek 9/30 官宣开源面向华为昇腾算力平台的基础设施组件，覆盖 TileLang 高级语言编译工具、计算库与分布式通信库，与英伟达平台组件「一一对应」：DeepGEMM、DeepEP、TileKernels、FlashMLA、DeepSelect。华为同步在 CANN 社区开源围绕昇腾 950 的合作成果，并披露 DeepSeek-V4.1-Flash 部分离线推理性能。

## 要点
- TileLang 昇腾版封装 Ascend C 底层指令、保住性能上限：DeepSeek 训练中的**每一个** TileLang 算子在昇腾上都有对应高性能实现；TileLang 已承载 V4 系列训练大部分算子
- 双方联合推进基于昇腾 950 的 128 卡超节点方案，对计算与通信做了深度优化——这是「模型团队反向定义芯片软件栈」路线的标志性一步
- 时间线：2025/8 V3.1 提 UE8M0 FP8 面向国产芯片 → 2025/9/29 TileLang-Ascend 开源 → 2026/4/24 V4 预览版带昇腾算子 → 本次成套开放基础设施层
- 动机面：DeepSeek 超 70% 算力用于训练，多硬件路线可摊薄未来平台切换的工程成本
- 关联 [[AI编译器2026格局]]、[[AI芯片推理硬件2026全景]]、[[推理引擎格局-vLLM-SGLang]]
