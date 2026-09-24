# M5 Ultra Mac Studio 开售：512GB 统一内存与分布式推理 3x

- 来源: https://www.apple.com/newsroom/2026/08/apple-introduces-new-mac-studio-with-m5-max-and-m5-ultra
- 日期: 2026-09-22
- 标签: #Apple-Silicon #MLX #本地推理

## 摘要
Apple 8/25 发布 M5 Max / M5 Ultra 版 Mac Studio，9/22 正式开售。M5 Ultra 最高 36 核 CPU、80 核 GPU、512GB 统一内存，峰值 AI 算力达 M3 Ultra 的 4.3x；每个 GPU 核内置 Neural Accelerator。Thunderbolt 5 支持多机集群，分布式推理最高达单机的 3x。

## 要点
- 内存上限实为 512GB（此前传闻 768GB 不成立），可单机装下超大 MoE；M5 Max 为 128GB / 最高 40 核 GPU
- AI 峰值：M5 Ultra 4.3x M3 Ultra、9.8x M1 Ultra；M5 Max 单机约 3.9x 上代，收益主要在 prompt processing
- Neural Accelerator 落在每个 GPU 核内，MLX 自动选内核、无需改代码（需 macOS 26.2+）
- TB5 集群 = 分布式推理 3x 的硬件前提，对应 JACCL / mlx.launch 路径
- 与 [[HBF-NAND-AI推理内存2026]] 互补：本地推理的容量瓶颈正被 UMA 与 Mac 集群同时抬高
- 关联 [[MLX生态2026]]
