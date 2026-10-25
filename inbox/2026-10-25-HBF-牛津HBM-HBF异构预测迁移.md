# HBM+HBF 异构内存：硬件托管预测迁移，单卡跑 9.2× 更大模型
- 来源: https://ieeexplore.ieee.org/document/11654188
- 日期: 2026-08-12
- 标签: #HBF #HBM #内存层级 #LLM推理

## 摘要
牛津（Atassi / Zilberman / Awad，IEEE CAL 25(2):371-374）量化了「用 HBF 顶替 HBM」的代价：HBF 单 stack 容量 512–1024GiB（约 16×）、带宽与 HBM 同量级，但读延迟约为 DRAM 的 1000×，朴素替换会因长尾延迟饿死 GPU warp 调度器。作者提出硬件托管的 HMA（HBM+HBF 混合）＋基于预测的迁移策略，把高延迟访问挤出关键路径，geo-mean 相对 HBF-only 提速 2.79×，同时单卡可容纳 9.2× 更大的模型。

## 要点
- 结论主线：HBF 解决的是「装不下」，不解决「跑得快」；必须靠 HBM 做热数据缓存层才有意义
- 关键机制不是软件换页，而是硬件管理 + 参数级预测迁移：在用到之前把热参数从 HBF 预取/迁到 HBM
- 评测模型含 MiniMax-M2.5、GLM-4.7、Qwen-30B、Qwen-397B，面向单张 edge GPU 场景（ARIA 资助）
- 与 SK hynix H³（同片 interposer 上 HBM+HBF）思路同源：异构内存分层 + 硬件/近存管理，而非把 NAND 当 SSD 用
- 待验证：性能数字来自体系结构仿真，HBF 尚无出货硅（SanDisk 首批样品 2026H2、整机 2027 初）
