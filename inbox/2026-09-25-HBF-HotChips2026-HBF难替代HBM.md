# Hot Chips 2026：HBF 容量惊人但带宽只有 HBM 的 0.6×，不能普遍替代 HBM

- 来源: https://chipsandcheese.com/p/hot-chips-2026-applying-high-bandwidth
- 日期: 2026-08-23
- 标签: #HBF #HBM #内存层级 #HotChips #AI推理

## 摘要
Hot Chips 2026 tutorials 日（8/23）Anurag Agarwal 与 Radhakrishna Giduthuri 讲了 HBF 如何落到 ML 负载；同期 GPU IP 公司 OXMIQ 的仿真给出冷水结论：HBF 提供约 14× 容量，但聚合带宽只有 HBM 的 0.6×，负载一旦转成带宽敏感型，大容量无法弥补带宽赤字。HBF 的价值集中在「巨大但偏冷」的数据层。

## 要点
- 形态像 HBM 立方体（与 HBM 并排同封装），内部却是 NAND：软件要用 DMA 在 HBF 与 DRAM 间搬数据，且必须大块对齐访问，等于把 SSD 集成到处理器旁
- HBF 不是 Optane 那种可当另一池内存使用的路线，host 侧还要接管写均衡等 SSD 控制器职责，编程模型更接近块设备
- Sandisk 口径仍是乐观面：宣称带宽接近 HBM、容量 8–16× 且成本相近，Llama 3.1 405B 读权重仿真与「无限容量 HBM」只差 2.2%
- 结论分歧的本质是负载画像：权重读取/冷数据集适合 HBF 分层，高并发 decode 这类带宽饥饿型负载用 HBF 反而更差
- 关联 [[HBF-NAND-AI推理内存2026]] [[AI芯片推理硬件2026全景]]
