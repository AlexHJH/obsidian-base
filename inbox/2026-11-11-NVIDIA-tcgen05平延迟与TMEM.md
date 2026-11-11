# tcgen05 的单指令延迟是平的：11 拍打满 m64→m256，TMEM 换掉了 255 寄存器上限
- 来源: https://arxiv.org/html/2512.02189v3（IPDPS 2026，UDel CRPL 微基准）
- 日期: 2026-11-11（论文 v3 阅读；数据表 V）
- 标签: #NVIDIA #Blackwell #tcgen05 #TMEM #TensorCore

## 摘要
同一组「累加器依赖链」微基准下，Hopper `wgmma` 的单指令延迟随 tile 宽度线性涨（m64n64k16 32 拍 → m64n256k16 128 拍），Blackwell `tcgen05.mma` 却几乎不变：m64n64k16 11.0、m128n128k16 11.3、m256n256k16 11.4 拍，同比降 2.9–11.6×。原因不是流水更宽，而是执行粒度从 warpgroup（128 线程）收成 warp（32 线程）+ 结果不再落寄存器。

## 要点
- 为什么必须搬去 TMEM：单 CTA 最大原子 m128n256k16 的累加器是 32768 个 FP32，摊到一个 warpgroup 就是 256 寄存器/线程，比 255 上限多一个 —— 旧编程模型物理上装不下
- TMEM 规格：每 SM 256KB（512 列 × 128 lane × 32bit），读 16 TB/s、写 8 TB/s per SM，与 SMEM 带宽相加而非共用
- SASS 层面 `tcgen05.mma` 按精度分派 DMMA/HMMA/QMMA/OMMA/IMMA；Hopper 是统一 `wgmma` 再分 HGMMA/QGMMA/IGMMA，FP4 的 OMMA 在 Hopper 无对应
- 判读：延迟平坦意味着「加深 stage 数掩盖宽 tile」这套调参法失效，瓶颈从寄存器压力转到 TMEM 容量/带宽与 mbarrier 级数；配 [[NVIDIA-Tensor-Core-TMA与AI指令体系]] 的 2SM 变体看，跨 CTA 时延也要重测
- 口径：延迟为论文表 V 的 SI-LAT（累加器携带依赖），非吞吐；TMEM 访问 ≈420 拍 vs H200 ≈1000 的数字来自第三方转述，未读原文对应表

关联：[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[AI芯片推理硬件2026全景]]、[[MLSys2026论文精选]]
