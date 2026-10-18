# 把 HBF 当「主显存」的首次系统评估：容量能买 batch，耐久是硬墙
- 来源: https://arxiv.org/abs/2608.13868 （DOI 10.1109/LCA.2026.3705817）
- 日期: 2026-08-14
- 标签: #HBF #内存墙 #KV缓存 #LLM服务 #耐久度

## 摘要
IEEE CAL 短文（Son 等，含 Onur Mutlu）首次把 HBF 当作 GPU **主存**来系统性评估——权重和 KV cache 全放 NAND，读写都走闪存，而不是只当 KV 卸载层。结论：容量红利真实（batch 涨 1.3–5.3×、最小 GPU 数从 4 卡压到 1 卡），但前提是单堆读带宽真能维持 HBM4 同档的 1.6 TB/s；一旦读带宽打折，容量就白给。真正的墙在写入侧：KV 写入让 NAND 三年内 P/E 次数普遍远超 100K。

## 要点
- 五套配置（同一 GPU core）：HBM4 8×(36GB/1.6TB/s)=288GB；HBF 1 HBM+7 HBF=3620GB，读 11.2 / 写 0.112 TB/s；HBF+ 8 堆全 HBF=4096GB，读 12.8 / 写 0.128
- 读带宽是命门：CONV/CONV+（容量给 HBF、带宽按常规 SSD 级）batch 甚至小于 HBM4，容量利用率最多浪费 95%
- 收益：8 卡下 HBF / HBF+ 的 per-GPU batch 比 HBM4 高 1.3–4.5× / 1.7–5.3×；HBM4 至少 4 卡才能服务两个模型，HBF 单卡即超过 8 卡 HBM4
- 上下文越长越吃香：HBF+ 在 Mid/Long 相对 Short 的增益再高 124% / 200%
- 中间数据必须挪出 NAND：写中间数据会让 decode 每步写流量最多涨 100×，故 HBF 保留 1 个 HBM 堆、HBF+ 改用逻辑 die 上 40MB SRAM（每卡 320MB），仍能撑 327 的 batch
- KV 写入占 Llama4 decode 时间 5–13.9%，短上下文 / 多卡 / MoE 更重；SLO 越松收益越大（offline 16 卡 Llama4 增益 4.1%→14.8%）
- 耐久：3 年保修期内 P/E 次数多数场景远超 100K（SLC 上限），relaxed-retention 写法只算缓解
- 判据：HBF 的正确叙事是「用容量买 batch、用少卡换成本」，不是替代 HBM；落地门槛 = 读带宽达标 + 写性能/耐久各提升一个量级以上
- 关联 [[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]
