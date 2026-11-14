# FlexiCache：按 attention head 的时间稳定性分层放 KV

- 来源: https://arxiv.org/abs/2511.00868 (MLSys 2026)
- 日期: 2026-05-20
- 标签: #MLSys #KV-cache #LLM推理 #vLLM

## 摘要
观测到不同 KV head 的"关键页集合"随时间稳定性差别极大：部分 head 在相邻 decode step 反复看几乎同一批 top-K 页，另一些频繁漂移。FlexiCache 据此做 head 级分层——不稳定的整份 KV 留在 GPU，稳定的只留 top-K 页、其余下放 host 内存，并周期性重排把新晋页搬回。

## 要点
- 分类一次性离线做：最不稳定的 25% head 定为 unstable，其余 stable；GPU 侧同时对全部 head 只对 top-K 页做稀疏 attention，省算力。
- 与"永久驱逐"类方法的区别：被换出的页仍在 host 内存里，后来变重要还能取回——牺牲带宽换不丢信息。
- 数据（vLLM 实现）：长上下文请求 GPU 显存占用最多降 70%，离线吞吐 1.38–1.55×，在线 token 延迟改善 1.6–2.1×，长上下文/长生成精度保持。
- 隐含前提很硬：head 的稳定性是模型属性、可离线固化。这个前提已被 [[MLSYS-WakeKV-静态分类会过期]] 直接挑战。
