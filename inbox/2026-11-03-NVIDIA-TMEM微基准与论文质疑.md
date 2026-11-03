# Blackwell TMEM 微基准：420 周期延迟的实测，以及一篇公开质疑
- 来源: https://arxiv.org/abs/2512.02189
- 日期: 2026-11-03（论文 v3，原稿 2025-12-01）
- 标签: #NVIDIA #TensorCore #TMEM #tcgen05 #微基准

## 摘要
Jarmusch 与 Chandrasekaran（Delaware）用 pointer-chase 与依赖链方法测 B200：TMEM cache-miss 端到端约 420 周期，对比 Hopper 全局访存约 1000 周期（降 58%）；并称 TMEM 驱动 GEMM 在混合精度训练上相对 H200 有 1.85×（ResNet-50）/1.55×（GPT-1.3B）。

## 要点
- 方法价值在隔离：FP4(e2m1)/FP6(e3m2,e2m3) 用依赖链测真延迟，而不是用带宽峰值倒推
- 架构事实可比对：[SemiAnalysis](https://newsletter.semianalysis.com/p/dissecting-nvidia-blackwell-tensor) 实测 1SM MMA 的 M=64 只有约 50% 峰值、M=128 近 100%——半条 datapath，选 tile 形状时比"峰值算力"有用
- 可信度红旗：有人写了逐节反驳（https://girl.surgery/bad_paper），指出文中"TMEM 有独立地址生成单元把 tile 预取进 staging buffer"这类机制描述站不住
- 结论口径：延迟数字可作量级参考，机制解释要回到 PTX ISA 9.4 的 tcgen05.alloc/ld/st/cp/fence/commit 原文核对
- 待核：420 周期是 SMEM→TMEM 还是 TMEM→寄存器路径的哪一段，摘要没区分

关联：[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
