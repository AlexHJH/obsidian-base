# FlashAttention-4: 面向非对称硬件扩展的算法-内核流水线协同设计
- 来源: https://mlsys.org/virtual/2026/poster/3536
- 日期: 2026-07-22
- 标签: #MLSys #FlashAttention #Blackwell #GPU内核 #推理优化

## 摘要
Together AI / Dao-AILab 在 MLSys 2026 发布 FlashAttention-4，针对 Blackwell GPU 非对称硬件扩展（Tensor Core 翻倍但共享内存/指数单元未同步升级）重新设计注意力内核，B200 BF16 达 1613 TFLOPs/s（71% 利用率）。

## 要点
- 全异步 MMA + 更大 tile，利用 Tensor Memory 和 2-CTA MMA 模式减少 shared memory 流量
- 软件模拟指数 + 条件 softmax 缩放，削减非 matmul 操作占比
- B200 上比 cuDNN 9.13 快 1.3×，比 Triton 快 2.7×
- 揭示 Blackwell 新瓶颈：shared memory 带宽和指数单元成新制约
